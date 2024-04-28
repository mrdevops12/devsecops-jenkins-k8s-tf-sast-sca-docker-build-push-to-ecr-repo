pipeline {
  agent any
  tools { 
        maven 'Maven_3_6_3'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=testing-devsec -Dsonar.organization=testing-devsec -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=0c4aff24e69fdd93c950fa541bbfaee291f45b31'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
				withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://911269364388.dkr.ecr.ap-south-1.amazonaws.com', 'aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
