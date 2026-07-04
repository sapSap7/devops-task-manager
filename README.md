Part A - ECR (Image registry): 
  Push Docker images to AWS' private container registry, 
  so they're stored securely in the cloud and ready to be pulled by Kubernetes.

Part B - Kubernetes Cluster:
  Set up minikube locally and deploy entire app to it - frontend, backend and database all running as pods.

Part C - Kubernetes yaml files:
  secret.yaml - sensitive passwords stored securely in base64, not in plain text
  postgres-pvc.yaml - database data survives pod restarts thanks to persistent storage
  postgres-deployment.yaml - database pod with the pvc attached.
  backend-deployment.yaml - 3 pods with a /health readiness probe and rolling update strategy
  frontend-deploymeny.yaml - frontend pod
  ingress.yaml - only the frontend is exposed to the outside world.

Part D - Jenkins CI/CD pipeline:
  Every time code is being pushed to GitHub:
  1. GitHub sends a webhook to Jenkins (via ngrok).
  2. Jenkins automatically pulls the latest code.
  3. Builds fresh  Docker images tagged with the build number.
  4. Pushes them to ECR
  5. Updates the runnning Kubernetes deployments with zero downtime.
