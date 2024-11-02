MariaDB
=======

## Links
- https://artifacthub.io/packages/helm/bitnami/mariadb
- https://github.com/bitnami/charts/blob/main/bitnami/mariadb/values.yaml
- https://docs.bitnami.com/aws/infrastructure/lamp/administration/change-reset-password-mariadb/

## Usage
```bash
cd ~/workspace/prwnd9/mariadb

# https://kind.sigs.k8s.io/
kind create cluster --name test
kubectl cluster-info --context kind-test

# https://artifacthub.io/packages/helm/bitnami/mariadb
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
helm install mariadb bitnami/mariadb --version 19.1.0 -f values.yaml --wait

# update root pasword
# https://docs.bitnami.com/aws/infrastructure/lamp/administration/change-reset-password-mariadb/
kubectl exec -it mariadb-0 -- bash
/opt/bitnami/mariadb/bin/mysqladmin -p -u root password NewP@s5w0rd
exit
echo -n "NewP@s5w0rd" | base64
kubectl edit secret mariadb
kubectl delete pod mariadb-0
kubectl logs -f mariadb-0

# upgrade chart
ROOT_PASSWORD=$(kubectl get secret mariadb -o jsonpath="{.data.mariadb-root-password}" | base64 -d)
echo $ROOT_PASSWORD
helm upgrade mariadb bitnami/mariadb --set auth.rootPassword=$ROOT_PASSWORD -f values.yaml --wait

# check
kubectl exec -it mariadb-0 -- mysql -uroot -p

# clean up
helm uninstall mariadb
kind delete cluster --name test
```
