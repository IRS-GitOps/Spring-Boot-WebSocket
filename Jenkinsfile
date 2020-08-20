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
    stage('Maven Compile') {
      steps {
        container ('maven') {
          checkout scm
          sh 'mvn install'
          sh 'mvn compile'
        }
      }
    }
    stage('Sonar Scans') {
      steps {
        container('maven') {
                withCredentials([string(credentialsId: 'Sonar_Login', variable: 'Sonar_Login'), string(credentialsId: 'Sonar_URL', variable: 'Sonar_URL'), string(credentialsId: 'Sonar_Project', variable: 'Sonar_Project')]) {
                  sh 'mvn sonar:sonar   -Dsonar.projectKey=${Sonar_Project}   -Dsonar.host.url=${Sonar_URL}  -Dsonar.login=${Sonar_Login}'
                  //sh 'mvn sonar:sonar   -Dsonar.projectKey=IRS-GitOps   -Dsonar.host.url=https://sonarqube.cb-demos.io   -Dsonar.login=d1808d0a6091d51785fc8fe681150dbadf43fa0a'
                }  
                
          }
      }
    }  
    stage('Deploy to Nexus'){
      //when {
       // branch 'master'
      //}
      steps {
        container('maven') {
          input(message: "Should we deploy?", ok: "Deploy", submitterParameter: "APPROVER")
       nexusArtifactUploader artifacts: [[artifactId: 'spring-boot-starter-parent', classifier: '', file: 'target/spring-boot-websocket-0.0.1-SNAPSHOT.jar', type: 'jar']], credentialsId: 'ci-sa', groupId: 'org.springframework.boot', nexusUrl: 'nexus.cb-demos.io', nexusVersion: 'nexus3', protocol: 'https', repository: 'maven-releases', version: '0.0.1'
        }
      }
    }
  }  
  Post {
       always {
           cloudBeesFlowCallRestApi body: '', configuration: 'Thunder-CD', envVarNameForResult: '', httpMethod: 'POST', parameters: [[key: 'projectName', value: 'tjohnson Demo'], [key: 'releaseName', value: 'tj-Spring-Boot-WebSocket']], urlPath: 'https://cd.cb-demos.io/rest/v1.0/releases'
       }
  }
}

