pipeline {
    triggers {
  pollSCM '* * * * *'
}
agent any
tools {
    maven 'maven'
    jdk 'java'
}
    
    
   stages {
  stage('compile') {
    steps {
      git 'https://github.com/flaviano-ux/industry-grade-project-1'
      sh 'mvn compile'
    }

    post {
      always {
        recordIssues sourceCodeRetention: 'LAST_BUILD', tools: [pmdParser(pattern: '**/pmd.xml')]
      }
    }
  }

  stage('unittest') {
    steps {
    sh 'mvn verify'
     }  
    
    post {
      always {
        jacoco()
    }
  }
  }
  
  stage('package') {
    steps {
      sh 'mvn package'
    }
  }

 stage('Build Docker Image') {
            steps {
                sh 'docker build -t abc:v1-${BUILD_NUMBER} .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "DOCKER_HUB_LOGIN", url:""]) {
                    sh 'docker tag abc:v1-${BUILD_NUMBER} flavian07/abc:v1-${BUILD_NUMBER}'
                    sh 'docker push flavian07/abc:v1-${BUILD_NUMBER}'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh 'docker run -d -P flavian07/abc:v1-${BUILD_NUMBER}'
            }
        }
    
        stage('deploy to kubernets cluster') 
                     {
	         steps {
                       sh '''
                   kubectl apply -f pod.yml
                   kubectl apply -f service.yml
                   kubectl apply -f deployment.yml
                    kubectl apply -f probe.yml
                          '''
                       }		
                      }
                      
                      stage('deploy-QA')
                         {
	         steps {
                    sh script: 'sudo ansible-playbook --inventory /tmp/myinv $WORKSPACE/deploying-kube.yml --extra-vars "env=qa build=$BUILD_NUMBER"'
           }		
        }       
    }
 }
