# Creating Helm Repositories for Payments and Shipping Services

## Scenario

As a DevOps engineer at an e-commerce company, you manage two services: Payments and Shipping. To streamline deployment and management, we will create a Helm repository containing Helm charts for both services. We will use a dummy BusyBox image for both services to simulate their functionality.

---

### Prerequisites

* Helm installed on your system.
* A running Kubernetes cluster.
* GitHub account for hosting the Helm repo.
* kubectl configured to interact with the cluster.

---

## Step 1: Create the Helm Charts

1. **Create the directory structure:**

   ```bash
   mkdir -p helm-repo/{payments,shipping}
   cd helm-repo
   ```

2. **Generate Helm charts for both services:**

   ```bash
   helm create payments
   helm create shipping
   ```

---

## Step 2: Customize the Helm Charts

### BusyBox Deployment Template:

Here is the complete deployment template file for the BusyBox service:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}-{{ .Chart.Name }}
  labels:
    app: {{ .Chart.Name }}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: {{ .Chart.Name }}
  template:
    metadata:
      labels:
        app: {{ .Chart.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
          command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
          imagePullPolicy: {{ .Values.image.pullPolicy }}
```

### Payments Chart:

1. Update the `values.yaml` file in the payments chart:

   ```yaml
   image:
     repository: busybox
     tag: latest
     pullPolicy: IfNotPresent
   appMessage: "Payments Service"
   ```

2. Update the deployment template (`templates/deployment.yaml`):

   ```yaml
   spec:
     containers:
       - name: payments
         image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
         command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
   ```

### Shipping Chart:

1. Update the `values.yaml` file in the shipping chart:

   ```yaml
   image:
     repository: busybox
     tag: latest
     pullPolicy: IfNotPresent
   appMessage: "Shipping Service"
   ```

2. Update the deployment template (`templates/deployment.yaml`):

   ```yaml
   spec:
     containers:
       - name: shipping
         image: {{ .Values.image.repository }}:{{ .Values.image.tag }}
         command: ['sh', '-c', 'echo {{ .Values.appMessage }}; sleep 3600']
   ```

---

## Step 3: Package the Charts

1. Package the payments chart:

   ```bash
   helm package payments
   ```

2. Package the shipping chart:

   ```bash
   helm package shipping
   ```

3. Create an index file:

   ```bash
   helm repo index .
   ```

<img width="1391" height="840" alt="image" src="https://github.com/user-attachments/assets/5c33008a-d791-43ba-a8cb-90d8a78c87f5" />


---

## Step 4: Host the Helm Repo on GitHub

1. **Create a new GitHub repository:**

   * Name: `helm-repo`

<img width="1916" height="797" alt="image" src="https://github.com/user-attachments/assets/4fd35bab-2f1c-418a-903d-61bc9407decd" />


2. **Initialize a Git repo:**

   ```bash
   git init
   git remote add origin https://github.com/username/helm-repo.git
   ```

<img width="1211" height="707" alt="image" src="https://github.com/user-attachments/assets/d0932934-249d-43da-ad29-837f4afab5ac" />


3. **Push the Helm charts to GitHub:**

   ```bash
   git add .
   git commit -m "Add payments and shipping charts"
   git push -u origin main
   ```

   <img width="1148" height="977" alt="image" src="https://github.com/user-attachments/assets/1995e704-0e10-438b-b662-314cb4daa06b" />


4. **Configure GitHub Pages:**

   * Go to the repo settings.
   * Enable GitHub Pages from the `main` branch.

<img width="1918" height="842" alt="image" src="https://github.com/user-attachments/assets/f84e4370-46e7-4d7f-a5c6-383e0c136859" />

- save it

<img width="1913" height="876" alt="image" src="https://github.com/user-attachments/assets/009d1d5c-5406-47d1-8991-94589f6c68c2" />

<img width="1300" height="602" alt="image" src="https://github.com/user-attachments/assets/796a68de-5e69-459f-a125-518a8e9d404a" />

---

## Step 5: Using the Helm Repo

1. **Add the Helm repo:**

   ```bash
   helm repo add myrepo https://username.github.io/helm-repo
   helm repo update
   ```

   <img width="1165" height="383" alt="image" src="https://github.com/user-attachments/assets/a0b26856-e321-4ca4-a593-5277867f5402" />


2. **Search for charts:**

   ```bash
   helm search repo myrepo
   ```

<img width="1918" height="302" alt="image" src="https://github.com/user-attachments/assets/ed843a87-a220-43ac-9b59-749dd32f40ea" />


3. **Install a service (e.g., payments):**

   ```bash
   helm install payments-service myrepo/payments
   ```

---
