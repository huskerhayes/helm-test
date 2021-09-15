# helm-test

## Creating GKE Cluster
GKE cluster was using the $300 free credit offered by Google. The cluster was created using the provided GUI in the GCP console. 
This can be done by clicking on Kubernetes Engine, clicking create and then using GKE Autopilot to automatically launch a cluster with no configuration required.

## Connecting to GKE Cluster
1. Install gcloud CLI (https://cloud.google.com/sdk/gcloud/)
2. Authenticate gcloud to GCP account with the following command.

    `gcloud auth login`
3. Connect to Kubernetes cluster by using this command replacing $CLUST with the name of the cluster and $CLUST_REG with the region of the cluster
   
    `gcloud container clusters get-credentials $CLUST --zone $CLUST_REG`

4. To see all clusters currently available to you, open a terminal and use this command
   
    `kubectl config get-clusters`

5. Switch to the GKE cluster you just created by finding the correct context for the cluster name from the output of step 4 and using the following command
   
    `kubectl config get-contexts`
    
    `kubectl config use-context $CLUST_CONTEXT `

6. Verify you are using the correct context with the following command. All kubectl commands sent will apply to this context/cluster so ensure it is the correct one.
   
    `kubectl config current-context`

## Install Helm
1. Download Helm (https://github.com/helm/helm/releases)
2. Unzip it
3. Run the client
4. Verify Helm is installed by using a Helm command ex: 
   
   `helm help`

## Creating the Helm Chart
1. Create a new Helm chart using the following command
    
    `helm create $CHART_NAME`

2. Open the values.yaml file that was generated and change the repository value to the image you would like to use. For this example I am using a Spring pet clinic sample image at paulczar/spring-petclinic

```image:
  repository: paulczar/spring-petclinic
  pullPolicy: IfNotPresent
  tag: latest
```
3. Open the templates folder and then open the deployment.yaml file. Change the containerPort to whatever port your application uses. The pet clinic app uses 8080.
   ```
    ports:
       - name: http
        containerPort: 8080
        protocol: TCP```
        
## Install Helm Chart
1. The Helm chart is now ready to install. Open a terminal and navigate to the Helm chart directory and run the following command. Use the second command if you want to install the chart into a specific namespace

   `helm install pet-clinic ./`
   
   `helm install pet-clinic ./ -n $NAMESPACE`

2. Verify the chart has been deployed and no issues are occuring by performing a get all. Use the second command if you installed into a specific namespace.

    `kubectl get all`
    
    `kubectl get all -n $NAMESPACE`

3. Follow the instructions given in the terminal to port forward and access the web app you have deployed. 

  ```export POD_NAME=$(kubectl get pods --namespace pet-clinic -l "app.kubernetes.io/name=petclinic,app.kubernetes.io/instance=pet-clinic" -o jsonpath="{.items[0].metadata.name}")
  export CONTAINER_PORT=$(kubectl get pod --namespace pet-clinic $POD_NAME -o jsonpath="{.spec.containers[0].ports[0].containerPort}")
  kubectl --namespace pet-clinic port-forward $POD_NAME 8080:$CONTAINER_PORT
  ```


### References

http://docs.shippable.com/deploy/tutorial/deploy-to-gcp-gke-helm/ (note Helm 3.0 does not use the init command shown here)
https://docs.bitnami.com/tutorials/create-your-first-helm-chart/
