# TerraformwithKubernetes
Deploy in Jenkins MasterSlave in Kubernetes(Minikube) using Terraform

1. Install Docker & MiniKube:

    Download the latest version of Docker using following command:
    
        sudo apt-get update && sudo apt-get install docker.io -y
        
    Add Local User to Docker Group:
        
        sudo gpasswd -a ubuntu docker
        
    Update Permission of Docker.sock File:
    
        sudo chmod 666 /var/run/docker.sock
 
    Download the latest version of MiniKube using following command:
    
          curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
        
    Check MiniKube Version:
    
          minikube version
        
    Switch to Root User for Installation:
    
          sudo su
        
    Installed Minikube with --vm-driver=none because we are getting virtualisation on VM:
    
          minikube start --vm-driver=docker
      
    Command to Check Installation Status:
    
          minikube status
    
2. Check Kubernetes Nodes, Pods and Cluster Details:

    Configure Admin.conf file:

          sudo cp /etc/kubernetes/admin.conf $HOME/
          sudo chown $(id -u):$(id -g) $HOME/admin.conf
          export KUBECONFIG=$HOME/admin.conf
        
    Health Check Commands:
     
         kubectl get nodes  
	     kubectl get pods --all-namespaces
	     kubectl cluster-info
	     kubectl config get-contexts
        
