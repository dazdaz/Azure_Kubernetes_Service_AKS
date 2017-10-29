<pre>
# Introducing AKS (managed Kubernetes) and Azure Container Registry improvements
# https://azure.microsoft.com/en-us/blog/introducing-azure-container-service-aks-managed-kubernetes-and-azure-container-registry-geo-replication/

# 29th Oct 2017
# https://docs.microsoft.com/en-us/azure/aks/ # Docs on AKS
# Kubernetes Version 1.7.7 deployed by default

# westus2 / ukwest
LOCATION=ukwest
RG=daz-mngk8s-rg
CLUSTERNAME=daz-mngk8s

az group create --name $RG --location $LOCATION
az aks create --resource-group $RG --name ${CLUSTERNAME} --agent-count 2 -s Standard_D2_v2
# Download and install kubectl
sudo az aks install-cli
kubectl get nodes
# Downloads and merge credentials into ~/.kube/config
az aks get-credentials -n ${CLUSTERNAME} -g $RG

# Display version and state of Azure Managed k8s cluster
az aks show -g $RG -n ${CLUSTERNAME} -o table

# Build out a total of 3 Agent VM's to run out containers
az aks scale -g $RG -n ${CLUSTERNAME} --agent-count 3

# Check what version of Azure Managed k8s is available
az aks get-versions -g $RG -n $CLUSTERNAME -o table
az aks upgrade -g $RG -n $CLUSTERNAME -k 1.8.1
# Check that our nodes have been upgraded to 1.8.1
kubectl get nodes
kubectl version

# Access k8s GUI, setup SSH Tunelling in your SSH Client
kubectl get pods --namespace kube-system | grep kubernetes-dashboard
kubernetes-dashboard-3427906134-9vbjh   1/1       Running   0          49m
kubectl -n kube-system port-forward kubernetes-dashboard-1427906131-8vbjh 9090:9090

# Install reverse proxy, show IP on LoadBalancer
helm init
helm install stable/nginx-ingress
kubectl --namespace default get services -o wide -w flailing-hound-nginx-ingress-controller

# k8s-cron-jobs required k8s 1.8 https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/
# kubectl run cronjobname --schedule "*/5 * * * *" --restart=OnFailure --image "imagename" -- "command"

# az aks delete --resource-group $RG --name ${CLUSTERNAME} --yes
# az group delete --name $RG --no-wait --yes
</pre>
