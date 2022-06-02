// global variables section
// defining the artifactory server instance
def server = Artifactory.server "artifactory-service"

pipeline{
    agent any
    stages {
        stage('poll') {
            checkout scm
        }
        stage('Build & Unit test') {
            steps {
                sh 'mvn clean verify -DskipITs=true'
                junit '**/target/surefire-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
        }
        stage('Static Code Analysis') {
            steps {
            // The -Dsonar.projectName=example-project option is the option to pass the
            // SonarQube project name.
            // In this way, all our results will be visible under the
            // projectName=example-project that we created earlier in sonarqube
            sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project -Dsonar.projectKey=example-project -Dsonar.projectVersion=$BUILD_NUMBER';
            }
        }
        stage('Integration Test'){
            steps {
                sh 'mvn clean verify -Dsurefire.skip=true';
                junit '**/target/failsafe-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
        }
        stage('Publish') {
            steps {
                // To upload the build artifacts to Artifactory, we will use the File Specs. 
                def uploadSpec = """{
                    "files": [
                        {
                            "pattern": "target/hello-0.0.1.war",
                            "target": "helloworld-greeting-project/${BUILD_NUMBER}/",
                            "props": "Integration-Tested=Yes; Performance-Tested=No"
                        }
                    ]
                }"""
                // uploading the fileSpec variable to artifact server
                server.upload(uploadSpec)
            }
        }
    }
}
