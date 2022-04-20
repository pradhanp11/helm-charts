# HashiCorp Vault
Source: https://www.vaultproject.io/
Helm Chart Source: https://artifacthub.io/packages/helm/hashicorp/vault
Good Document: https://learn.hashicorp.com/tutorials/vault/kubernetes-raft-deployment-guide?in=vault/kubernetes



Installation Process:
    
    // Create namespace
    #  kubectl create namespace vault
    
    // Add Repo
    #  helm repo add hashicorp https://helm.releases.hashicorp.com

    // Install Vault
    # helm install vault -f vault-values.yaml hashicorp/vault --namespace vault

    // Check the pod
    # kubectl get pods --selector='app.kubernetes.io/name=vault' --namespace='vault'
        NAME      READY   STATUS    RESTARTS   AGE
        vault-0   0/1     Running   0          43s

    // Initialize one Vault server with the default number of key shares and default key threshold
    # kubectl -n vault exec --stdin=true --tty=true vault-0 -- vault operator init
        Unseal Key 1: Ci7Fwl4GIJi53Je+OBlpNYyPrYNQwPnGXqUkOMu0/zEy
        Unseal Key 2: aM+a6fA/Bw0LDWlTL++CEkOyfWDSJepEgRuh8uwN7FXO
        Unseal Key 3: Y3FEXoIcqk1w3cNXH0n89043wvBblnnA4ZeFXQCSqEZq
        Unseal Key 4: l6LqH+uFzd9G4wiRhUqqy635f5nc03bA0C3tiRROF+kU
        Unseal Key 5: u9LZm6bM3tlaf0OlDsOaEswCcOO0m4p3Z3rvKB8I+vI9

        Initial Root Token: s.VQoblwaEB9eLYQocd95VN7rK

        Vault initialized with 5 key shares and a key threshold of 3. Please securely
        distribute the key shares printed above. When the Vault is re-sealed,
        restarted, or stopped, you must supply at least 3 of these keys to unseal it
        before it can start servicing requests.

        Vault does not store the generated master key. Without at least 3 keys to
        reconstruct the master key, Vault will remain permanently sealed!

        It is possible to generate new unseal keys, provided you have a quorum of
        existing unseal keys shares. See "vault operator rekey" for more information.

    // Unseal the Vault server with the key shares until the key threshold is met:
    //Unseal Key 1
    # kubectl -n vault exec --stdin=true --tty=true vault-0 -- vault operator unseal Ci7Fwl4GIJi53Je+OBlpNYyPrYNQwPnGXqUkOMu0/zEy  
        Key                Value
        ---                -----
        Seal Type          shamir
        Initialized        true
        Sealed             true
        Total Shares       5
        Threshold          3
        Unseal Progress    1/3
        Unseal Nonce       97712e2d-664f-3da4-79e1-4dc77276bb98
        Version            1.9.2
        Storage Type       file
        HA Enabled         false
    
    //Unseal Key 2
    # kubectl -n vault exec --stdin=true --tty=true vault-0 -- vault operator unseal aM+a6fA/Bw0LDWlTL++CEkOyfWDSJepEgRuh8uwN7FXO
        Key                Value
        ---                -----
        Seal Type          shamir
        Initialized        true
        Sealed             true
        Total Shares       5
        Threshold          3
        Unseal Progress    2/3
        Unseal Nonce       97712e2d-664f-3da4-79e1-4dc77276bb98
        Version            1.9.2
        Storage Type       file
        HA Enabled         false
    
    //Unseal Key 3
    # kubectl -n vault exec --stdin=true --tty=true vault-0 -- vault operator unseal Y3FEXoIcqk1w3cNXH0n89043wvBblnnA4ZeFXQCSqEZq
        Key             Value
        ---             -----
        Seal Type       shamir
        Initialized     true
        Sealed          false
        Total Shares    5
        Threshold       3
        Version         1.9.2
        Storage Type    file
        Cluster Name    vault-cluster-07200545
        Cluster ID      f3ad6eec-c6fb-dc3a-d2ba-194638cc578f
        HA Enabled      false
    
    // Check pod status again
    # kubectl get pods --selector='app.kubernetes.io/name=vault' --namespace='vault'
        NAME      READY   STATUS    RESTARTS   AGE
        vault-0   1/1     Running   0          12m
    

    Use WebUI to interface
    # kubectl get services -n vault
        NAME                       TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                         AGE
        vault-internal             ClusterIP   None             <none>        8200/TCP,8201/TCP               15m
        vault-agent-injector-svc   ClusterIP   10.152.183.202   <none>        443/TCP                         15m
        vault                      NodePort    10.152.183.12    <none>        8200:32117/TCP,8201:31411/TCP   15m
        vault-ui                   NodePort    10.152.183.211   <none>        8200:31909/TCP                  15m
    
    // Browse to vault-ui IP:Port
