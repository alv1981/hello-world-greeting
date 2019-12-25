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
     
      //def  scannerHome = tool 'Sonar-canner'
    
     //withSonarQubeEnv('sonarqube-server'){
      
      //sh "${scannerHome}/sonar-scanner"
       withSonarQubeEnv(credentialsId:'sonar_token',installationName:'sonarqube-server') {
              sh 'mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:3.6.0.1398:sonar -Dsonar.projectBaseDir=/home/jenkins/workspace/linux_academi/Pipeline_maven -Dsonar.sources=. -Dsonar.host.url=http://10.168.0.11:9000 -Dsonar.projectName=maven -Dsonar.projectVersion=1.0 -Dsonar.projectKey=maven'
         
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
