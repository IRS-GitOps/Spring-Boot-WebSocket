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
          withSonarQubeEnv('beedemo') {
              container('maven-jdk9') {
                  sh 'mvn -Dsonar.scm.disabled=True -Dsonar.login=$SONAR -Dsonar.branch=$BRANCH_NAME sonar:sonar'
                  clean verify -P sonar -Dsonar.login=$SONAR_LOGIN_TOKEN
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
