pipeline {
  agent {
    kubernetes {
      yamlFile 'maven-pod.yml'
    }
  }  
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
    preserveStashes(buildCount: 2)
  }
  stages('Sonar Scan & Maven Compile/Deploy to Nexus')
  {
    stage('Sonar Scans') {
      steps {
              container('maven') {
                checkout scm
                withCredentials([string(credentialsId: 'Sonar_Login', variable: 'Sonar_Login'), string(credentialsId: 'Sonar_Login', variable: 'Sonar_URL'), string(credentialsId: 'Sonar_Login', variable: 'Sonar_Project')]) {
                  sh """
                  mvn sonar:sonar \  
                  -Dsonar.projectKey=${Sonar_Project} \  
                  -Dsonar.host.url=${Sonar_URL} \
                  -Dsonar.login=${Sonar_Login} 
                  """
                }  
          }
      }
    }  
    stage('Maven Compile') {
      steps {
        container ('maven')
          sh 'mvn install'
          sh 'mvn compile'
      }
    }
    stage('Deploy to Nexus'){
      when {
        branch 'master'
      }
      steps {
        container('maven')
          input(message: "Should we deploy?", ok: "Deploy", submitterParameter: "APPROVER")
          sh 'mvn clean deploy'
      }
    }
  }  
}

