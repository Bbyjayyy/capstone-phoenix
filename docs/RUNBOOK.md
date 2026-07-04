# TaskApp Runbook

## 1. Connect to the cluster

Open the SSH tunnel to the k3s API server:

```bash
ssh -i ~/downloads/capstone-phoenix-key.pem -L 6443:127.0.0.1:6443 ubuntu@<CONTROL_PLANE_PUBLIC_IP>
In another terminal:
export KUBECONFIG=~/downloads/k3s.yaml
kubectl get nodes
2. Deploy the application
kubectl apply -f manifests/namespace.yaml
kubectl apply -f manifests/secret.yaml
kubectl apply -f manifests/postgres/
kubectl apply -f manifests/backend/
kubectl apply -f manifests/frontend/
kubectl apply -f manifests/ingress.yaml
3. Verify status
kubectl get nodes -o wide
kubectl get pods -n taskapp -o wide
kubectl get svc -n taskapp
kubectl get ingress -n taskapp
4. Test DNS
kubectl run dns-test -n taskapp --rm -it --image=busybox:1.36 --restart=Never -- nslookup postgres.taskapp.svc.cluster.local
5. Test Postgres
kubectl run pg-test -n taskapp --rm -it --image=postgres:16 --restart=Never -- psql "postgresql://taskapp:<PASSWORD>@postgres.taskapp.svc.cluster.local:5432/taskapp" -c "\l"
6. Test the frontend locally
kubectl port-forward -n taskapp svc/frontend 8080:80
Open:
http://localhost:8080
7. Restart a deployment
kubectl rollout restart deployment/backend -n taskapp
kubectl rollout status deployment/backend -n taskapp
8. Roll back a deployment
kubectl rollout history deployment/backend -n taskapp
kubectl rollout undo deployment/backend -n taskapp
9. Recover from backend failure
kubectl describe pod -n taskapp -l app=backend
kubectl logs -n taskapp deployment/backend
kubectl rollout restart deployment/backend -n taskapp
10. Recover from worker failure
kubectl get nodes
kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data
kubectl get pods -n taskapp -o wide
kubectl uncordon <node-name>
