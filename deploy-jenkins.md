## Jenkins in Kubernetes with Helm and Jenkins Kubernetes Plugin

We're going to use [Helm](https://github.com/kubernetes/helm) to install Jenkins in Kubernetes cluster.

1. Make sure you have helm binary installed on your machine, follow instructions [here](https://github.com/kubernetes/helm#install)

2. Install **tiller** (server part of Helm) to your Kubernetes cluster with:
  * `helm init`

3. Update helm repositories with:
  * `helm repo update`

4. Use helm to install Jenkins with value overrides from [values.yaml](./values.yaml):
  * `helm install --wait --name cloud-ci -f values.yaml stable/jenkins`
  Provided value overrides take care of switching Jenkins Master image to latest LTS version,
  creating proper Service Account credentials and providing them to Kubernetes plugin,
  updating plugins and bootstrapping an example job.

5. Use kubectl to wait for the Load Balancer provision:
  * `kubectl get svc --namespace default -w cloud-ci-jenkins`
  You'll need to use External IP provided in output to access your Jenkins at:
  http://external-ip:8080

6. Get Jenkins Admin password (randomly generated during installation) with:
  * `printf $(kubectl get secret --namespace default mendix-jenkins-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo`

7. Use the External IP, 'admin' username and password from the previous step to login to your Jenkins Master.
