This is a small guide on setting up your AKS Kubernetes cluster.

Guide assumes you have an active Azure subsctiption, configured Azure CLI and kubectl.

1. Ensure all required services are registered in Azure.
  * `az provider register -n Microsoft.Compute`
  * `az provider register -n Microsoft.Storage`
  * `az provider register -n Microsoft.Network`
  * `az provider register -n Microsoft.ContainerService`
  * Check registration state of each service with `az provider show -n Microsoft.Network | jq .registrationState`
    or `az provider show -n Microsoft.Network --out table`, wait till all of them are in **Registered** state.

2. Ensure you have a Resource Group created, you can create one with:
  * `az group create --name k8s-group --location centralus`
    (Note that AKS availability may differ in various locations)

3. Create Service Principal for Kubernetes cluster:
  * `az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/your-subscription-id"`
    Make a note of **appId** and **password** in output

4. Run AKS cluster deployment (I'm using 1 node due to Azure Free trial core quota):
    ```shell
    az aks create --verbose --resource-group k8s-grp --name k8s-CI-cluster \
    --ssh-key-value "$(cat ~/.ssh/id_rsa.pub)" --dns-name-prefix k8s-ci \
    --admin-username your_user --node-count 1 \
    --service-principal Service_Prinipal_appId \
    --client-secret Service_Principal_password
    ```

5. Get Kubernetes credentials:
  * `az aks get-credentials --resource-group k8s-group --name k8sCIcluster`

6. You should be able to see your cluster nodes with:
  * `kubectl get nodes`

7. Patch the existing **default** Storage Class to be used by default for Persistent Volume Claims without specified Storage Class:
  * `kubectl patch sc default -p '{"metadata":{"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'`

Now you can proceed with [Jenkins deployment](./deploy-jenkins.md).
