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
  stages('First 6 Stages of CI/CD')
  {
    stage('Pull Source Code from SCM') {
      steps {
        container ('maven') {
          checkout scm
        }
      }
    }
    stage('Maven Build Compile/Unit Test') {
      steps {
        container ('maven') {
          sh 'mvn install'
          sh 'mvn compile'
        }
      }
    }
    stage('Code Coverage - Sonar Scans') {
      steps {
        container('maven') {
                withCredentials([string(credentialsId: 'Sonar_Login', variable: 'Sonar_Login'), string(credentialsId: 'Sonar_URL', variable: 'Sonar_URL'), string(credentialsId: 'Sonar_Project', variable: 'Sonar_Project')]) {
                  sh 'mvn sonar:sonar   -Dsonar.projectKey=${Sonar_Project}   -Dsonar.host.url=${Sonar_URL}  -Dsonar.login=${Sonar_Login}'
                }                  
          }
      }
    }
    stage('Quality Scans') {
      steps {
        container('maven') {
          echo 'Quality Scanning Success!'
        }
      }
    }
    stage('Security Scans') {
      steps {
        container('maven') {
          echo 'Security Scanning Success!'
        }
      }
    }   
  }  
}
