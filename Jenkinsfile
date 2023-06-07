pipeline {
  agent any
  tools { 
        maven 'maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=techapp1 -Dsonar.organization=techapp1 -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=24e3dfe6c8f9354526b4ce1885d0fa017b395df6'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }	

// building docker image
stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("follyimage")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry("https://813817168689.dkr.ecr.us-east-1.amazonaws.com", "ecr:us-east-1:myawscredentials") 
			{
                    app.push("latest")
                    }
                }
            }
    	}



      // Kubernetes
  stage('Kubernetes Deployment of Easy Buggy Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'kubelogin']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}



  }
}
