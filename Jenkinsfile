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

}
}
