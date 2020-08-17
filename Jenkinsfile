pipeline {
  agent none
  options { 
    buildDiscarder(logRotator(numToKeepStr: '2'))
    skipDefaultCheckout true
    preserveStashes(buildCount: 2)
  }
  stages('Sonar Scan & Maven Compile/Deploy to Nexus)
  {
    stage('Sonar Scans') {
      agent {
        label 'sonar'
      }
      steps {
              container('maven-jdk9') {
                withCredentials([string(credentialsId: 'Sonar_Login', variable: 'Sonar_Login'), string(credentialsId: 'Sonar_Login', variable: 'Sonar_URL'), string(credentialsId: 'Sonar_Login', variable: 'Sonar_Project')]) {
                  sh '''mvn sonar:sonar   
                  -Dsonar.projectKey=${Sonar_Project}   
                  -Dsonar.host.url=${Sonar_URL}   
                  -Dsonar.login=${Sonar_Login}'''
                }  
          }
      }
    }  
    stage('Maven Compile') {
      agent
        label 'Maven'
      steps {
        sh 'mvn install'
        sh 'mvn compile'
      }
    }
    stage('Deploy to Nexus'){
      when {
        beforeAgent true
        branch 'master'
      }
      steps {
        input(message: "Should we deploy?", ok: "Deploy", submitterParameter: "APPROVER")
        sh 'mvn clean deploy'
        }
      }
    }  
  }
}
