podTemplate(label: 'mypod', serviceAccount: 'jenkins-admin', containers: [ 
    containerTemplate(
      name: 'kubectl', 
      image: 'alpine/k8s:1.26.1',
      resourceRequestCpu: '100m',
      resourceLimitCpu: '300m',
      resourceRequestMemory: '300Mi',
      resourceLimitMemory: '500Mi', 
      ttyEnabled: true, 
      command: 'cat'
    ) 
    
  ],

  
  ) {
    node('mypod') {

        stage('Get latest version of code') {
          
          checkout scm
        }
        stage('Create PV for NGINX') {
          
            container('kubectl') { 
                 sh 'kubectl create -f /home/jenkins/agent/workspace/nginx-task/nginx-pv.yml'                 
            }
            
        }  
        
        stage('Create PVC for NGINX') {
          
            container('kubectl') { 
                 sh 'kubectl create -f /home/jenkins/agent/workspace/nginx-task/nginx-pvc.yml'
            }
            
        }
        
        stage('Create NGINX StatefulSet') {
          
            container('kubectl') { 
                 sh 'kubectl create -f /home/jenkins/agent/workspace/nginx-task/nginx-statefulset.yml'
                 sh 'kubectl wait --for=condition=ready pod -l app=nginx -n default' 
            }
            
        }  
        
        stage('Create NGINX service') {
         
            container('kubectl') { 
                 sh 'kubectl create -f /home/jenkins/agent/workspace/nginx-task/nginx-svc'               
            }
            
        }  

        stage('Testing') {
            
           container('kubectl') { 
                 sh 'curl -v $(kubectl get pod web-0 -n default --template {{.status.podIP}})'               
            } 
            
        }

        stage('Closing'){
            
        }      
    }
}
