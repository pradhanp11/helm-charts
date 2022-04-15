# How to test Persisten Volume

1. Create a Persistent Volume Claim (PVC) and run it.

    cd helm-charts/nfs-storage/tests
    kubectl apply -f pvc.yaml

2. Create a Pod with PVC and run it.

    kubectl apply -f pvc-debugger.yaml

3. Execute into the pod

    kubectl exec -it volume-debugger sh

    / # cd /data
    /data # echo "test file" >test.txt 
    ''' this should create a test file in the NFS directory '''

4. Exit from pod

    /data # exit

5. Clean up

    kubectl delete pod volume-debugger
    kubectl delete pvc my-pvc
    kubectl get pod 
    kubectl get pvc
