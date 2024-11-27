# Kubernetes Production Setup Documentation

This document summarizes the steps taken to set up and validate the Production environment as part of the Kubernetes challenge.

---

# Step 1: Create the Production Namespace
(The production namespace was created to isolate resources)
command:
        kubectl create namespace production

# Step 2: Configure ConfigMap and Secret
*ConfigMap*
(The ConfigMap was created to store the APP_ENV variable for the production environment)
command:
        kubectl create configmap app-config \
        --from-literal=APP_ENV=production \
        --namespace=production

*Secret*
(The Secret was created to securely store the DB_PASSWORD for the production environment)
command:
        kubectl create secret generic app-secret \
        --from-literal=DB_PASSWORD=prodpassword123 \
        --namespace=production

# Step 3: Deploy the Application
(Create Deployment YAML)
command:
        sed -e 's/PLACEHOLDER_NAMESPACE/production/' \
            -e 's/replicas: .*$/replicas: 3/' app-deployment-template.yaml > production-deployment.yaml
(Apply Deployment)
command:
        kubectl apply -f production-deployment.yaml

# Step 4: Expose the Service
(The deployment was exposed as a NodePort service)
command:
        kubectl expose deployment nginx-app \
        --type=NodePort \
        --name=nginx-service \
        --port=80 \
        --target-port=80 \
        --namespace=production

# Step 5: Verify deployment by checking pods and services
command:
        kubectl get pods -n production
        kubectl get service -n production

# Step 6: Validate Environment Variables 
(Exec into a pod and verify)
command:
        kubectl exec -it <pod-name> -n production -- /bin/bash
        echo $APP_ENV
        echo $DB_PASSWORD