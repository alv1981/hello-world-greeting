node('maven') {
  stage('Poll') {
    checkout scm
  }
  stage('Build & Unit test'){
    sh "mvn -Dmaven.test.failure.ignore=true clean package"
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar'
  }
  stage('Static Code Analysis'){
     withSonarQubeEnv('sonarqube-server'){
      
       // sh 'mvn clean verify sonar:sonar -Dsonar.projectName=maven -Dsonar.projectKey=maven -Dsonar.projectVersion=$BUILD_NUMBER';
       sh 'mvn clean package sonar:sonar' 
     }
 }
       
  
  stage ('Integration Test'){
    sh 'mvn clean verify -Dsurefire.skip=true';
    junit '**/target/failsafe-reports/TEST-*.xml'
    archive 'target/*.jar'
  }
  stage ('Publish'){
    def server = Artifactory.server 'artifactory'
    def uploadSpec = """{
      "files": [
        {
          "pattern": "target/hello-0.0.1.war",
          "target": "maven/${BUILD_NUMBER}/",
          "props": "Integration-Tested=Yes;Performance-Tested=No"
        }
      ]
    }"""
    server.upload(uploadSpec)
  }
  stash includes: 'target/hello-0.0.1.war',
  name: 'binary'
}
