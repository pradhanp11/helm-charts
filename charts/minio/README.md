# MinIO 

Source: https://min.io

Helm Source Code: https://github.com/minio/minio/tree/master/helm/minio

Process to Start:

    # Create minio namespace
    pradhanp@lhotse:~/workspace/helm-charts/charts/minio$ kubectl create namespace minio
    namespace/minio created
    pradhanp@lhotse:~/workspace/helm-charts/charts/minio$ kubectl get namespace
    NAME              STATUS   AGE
    kube-system       Active   9d
    kube-public       Active   9d
    kube-node-lease   Active   9d
    default           Active   9d
    wordpress         Active   8d
    guestbook         Active   8d
    minio             Active   7s

    # Run minio
    pradhanp@lhotse:~/workspace/helm-charts/charts/minio$ helm install minio -f minio-values.yaml bitnami/minio --namespace minio
    WARNING: Kubernetes configuration file is group-readable. This is insecure. Location: /var/snap/microk8s/3052/credentials/client.config
    NAME: minio
    LAST DEPLOYED: Sat Apr 16 05:37:48 2022
    NAMESPACE: minio
    STATUS: deployed
    REVISION: 1
    TEST SUITE: None
    NOTES:
    CHART NAME: minio
    CHART VERSION: 11.2.9
    APP VERSION: 2022.4.12

    ** Please be patient while the chart is being deployed **

    MinIO&reg; can be accessed via port  on the following DNS name from within your cluster:

    minio.minio.svc.cluster.local

    To get your credentials run:

    export ROOT_USER=$(kubectl get secret --namespace minio minio -o jsonpath="{.data.root-user}" | base64 --decode)
    export ROOT_PASSWORD=$(kubectl get secret --namespace minio minio -o jsonpath="{.data.root-password}" | base64 --decode)

    To connect to your MinIO&reg; server using a client:

    - Run a MinIO&reg; Client pod and append the desired command (e.g. 'admin info'):

    kubectl run --namespace minio minio-client \
        --rm --tty -i --restart='Never' \
        --env MINIO_SERVER_ROOT_USER=$ROOT_USER \
        --env MINIO_SERVER_ROOT_PASSWORD=$ROOT_PASSWORD \
        --env MINIO_SERVER_HOST=minio \
        --image docker.io/bitnami/minio-client:2022.4.7-debian-10-r4 -- admin info minio

    To access the MinIO&reg; web UI:

    - Get the MinIO&reg; URL:

    echo "MinIO&reg; web URL: http://127.0.0.1:9001/minio"
    kubectl port-forward --namespace minio svc/minio 9001:9001
    pradhanp@lhotse:~/workspace/helm-charts/charts/minio$ 

User Name: minio-admin
Password: minio-pass

