# kubernetes_postgres_helm

```bash
helm repo add [repository-name] [repository-address]

helm repo add bitnami https://charts.bitnami.com/bitnami

helm repo update

```
____________________________________________________________________

Create and Apply Persistent Storage Volume
```bash
nano postgres-pv.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  name: postgresql-pv
  labels:
    type: local
spec:
  storageClassName: manual
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  hostPath:
    path: "/mnt/data"

kubectl apply -f postgres-pv.yaml

The resource itself.
The storage class.
The amount of allocated storage.
The access modes.
The mount path on the host system.
```
____________________________________________________________________

Create and Apply Persistent Volume Claim
```bash
nano postgres-pvc.yaml

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: postgresql-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
      
      
kubectl apply -f postgres-pvc.yaml

```
______________________________________________________________________

Install Helm Chart

```bash
Install the helm chart with the helm install command. Add --set flags to the command to connect the installation to the PVC you created and enable volume permissions:


helm install [release-name] [repo-name] --set persistence.existingClaim=[pvc-name] --set volumePermissions.enabled=true


helm install psql-test bitnami/postgresql --set persistence.existingClaim=postgresql_name_pvclaim --set volumePermissions.enabled=true


```
__________________________________________________________________________

```bash
export POSTGRES_PASSWORD=$(kubectl get secret --namespace default psql-test-postgresql -o jsonpath="{.data.postgresql-password}" | base64 --decode)

kubectl port-forward --namespace default svc/psql-test-postgresql 5432:5432

PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d postgres -p 5432


```




