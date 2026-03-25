---

## ☸️ Kubernetes (Self-Hosted kubeadm + Private Registry)

### Quick Deploy

```bash
# 1. Create namespace + secrets imperatively
kubectl create namespace book-service

kubectl create secret docker-registry registry-credentials \
  --namespace book-service \
  --docker-server=registry.yourdomain.com \
  --docker-username=YOUR_USER \
  --docker-password=YOUR_PASS

kubectl create secret generic book-service-secret \
  --namespace book-service \
  --from-literal=DATABASE_URL="postgresql://postgres:pass@postgres-svc.postgres.svc.cluster.local:5432/book_db" \
  --from-literal=JWT_SECRET="$(openssl rand -hex 64)" \
  --from-literal=S3_ACCESS_KEY="your-minio-access-key" \
  --from-literal=S3_SECRET_KEY="your-minio-secret-key"

# 2. Update image name in overlays
# Edit k8s/overlays/production/kustomization.yaml → images.name

# 3. Update S3_ENDPOINT and S3_PUBLIC_URL in configmap.yaml

# 4. Apply
kubectl apply -k k8s/overlays/production
```

### Verify

```bash
kubectl get all -n book-service
kubectl logs -n book-service -l app=book-service -f
kubectl get hpa -n book-service
```