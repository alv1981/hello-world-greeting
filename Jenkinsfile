node('maven') {
  stage('Poll') {
    checkout scm
  }
  stage('Build & Unit test'){
    sh "mvn -Dmaven.test.failure.ignore=true clean package"
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar'
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
}
