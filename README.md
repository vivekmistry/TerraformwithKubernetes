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
        
    Installed Minikube with --vm-driver=docker on bare metal server:
    
          	minikube start --vm-driver=docker
      
    Command to Check Installation Status:
    
          	minikube status
    
2. Check Kubernetes Nodes, Pods and Cluster Details & Install Helm Chart:

    Configure Admin.conf file:
		
		sudo cp /etc/kubernetes/admin.conf $HOME/
		sudo chown $(id -u):$(id -g) $HOME/admin.conf
		export KUBECONFIG=$HOME/admin.conf
        
    Health Check Commands:
    
    		kubectl get nodes  
		kubectl get pods --all-namespaces
	 	kubectl cluster-info
	     	kubectl config get-contexts
		
   Download the latest version of Helm & Change the File Permission, execute install file & install socat package:

		cd /tmp/
		curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > install-helm.sh
		chmod u+x install-helm.sh
		./install-helm.sh
		sudo apt-get -y install socat


   Create tiller service account && Create cluster role binding for tiller:
   
 		kubectl -n kube-system create serviceaccount tiller
		kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller


  
3. Install Terraform:
  
    First, install wget and unzip packages if they’re not already installed:
   	
		sudo apt update && sudo apt install wget unzip

    Go to the Terraform official website to download the latest version of Terraform:

		VERSION=0.12.25
		sudo wget https://releases.hashicorp.com/terraform/${VERSION}/terraform_${VERSION}_linux_amd64.zip -P /tmp
		
    Now, Unzip the Terraform zip file into the directory /usr/local/bin/ as below:
 
		sudo unzip /tmp/terraform_${VERSION}_linux_amd64.zip -d /usr/local/bin/

    Once you done, to verify that Terraform is installed properly, run the below command:
    
		terraform -v
		
		
4. Write TerraForm File & DownLoad HelmChart:

     Create one directory & terraform file:
 
 		mkdir terraform && cd terraform
		touch main.tf
		
     DownLoad Jenkins Helm Chart. Note, the serviceType is set of "NodePort" for minikube & addition key-values "NodePort: 32320" is added in values.yaml file:

		git clone ***************
		
		
     Edit Terraform File:
     
     		provider "kubernetes" {
  		  config_context_cluster   = "minikube"
		}
		
		resource "kubernetes_namespace" "1-minikube-namespace" {
		  metadata {
        		name = "my-first-terraform-namespace"
		   }
		}
		
		resource "helm_release" "local" {
  		  name          = "jenkins"
  		  chart         = "./jenkins"
		}

5. Execute TerraForm File:

		terraform init 
		terraform plan
		terraform apply --auto-approve
		

6. Follow the instructions that are printed from NOTES.TXT to access Jenkins:

    1. Get your 'admin' user password by running:
		
			printf $(kubectl get secret --namespace default setupjenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo
		
    2. Get the Jenkins URL to visit by running these commands in the same shell:
		  
		  export NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services setupjenkins)
		  export NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
		  echo http://$NODE_IP:$NODE_PORT/login

 7. Login to Jenkins & Create Test Job:
 
     Create Pipeline in Jenkins name "Test". In its Pipeline Section select below "Declarative Kubernetes" and copy below groovy scripts in it:
     
		      // Uses Declarative syntax to run commands inside a container.
		pipeline {
		    agent {
			kubernetes {
			    yaml '''
		apiVersion: v1
		kind: Pod
		spec:
		  containers:
		  - name: ubuntu
		    image: ubuntu
		    command:
		    - /bin/sh -c
		    args:
		    - cat
		'''
			    defaultContainer 'shell'
			}
		    }
		    stages {
			stage('Main') {
			    steps {
				sh 'hostname'
			    }
			}
		    }
		}
		
8. Execute Pipeline & Validate Results:

    On executing pipeline, the Jenkins Slave Pod will get created & execute content of groovy scripts:
    
    	ubuntu@ip-172-31-47-53:~/terraform/mychart$ kubectl get pods
		NAME                                READY   STATUS        RESTARTS   AGE
		createkubepod-1-023sw-v6n1d-9bfl7   2/2     Running       0          13s
		setupjenkins-665f9f9bf9-xrfjm       2/2     Running       0          6m52s

 
           
	   
 
 	



		
	
		
	

      


        
