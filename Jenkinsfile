pipeline{
    stages {
        stage('Build & Unit test') {
            steps {
                echo 'building and unit testing the application...'
                sh 'mvn clean verify -DskipITs=true'
                junit '**/target/surefire-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
        }
        stage('Static Code Analysis') {
            steps {
                sh 'mvn clean verify sonar:sonar -Dsonar.projectName=example-project
                -Dsonar.projectKey=example-project -
                Dsonar.projectVersion=$BUILD_NUMBER';
            }
        }
        stage ('Integration Test') {
            steps {
                sh 'mvn clean verify -Dsurefire.skip=true';
                junit '**/target/failsafe-reports/TEST-*.xml'
                archive 'target/*.jar'
            }
        }
        stage('Publish') {
            steps {
            // define the artifactory server
            // artifactory-service is the name of the artifactory server configured inside jenkins
            def server = Artifactory.server 'artifactory-service'
            def uploadSpec = """{
                "files": {
                    {
                        "pattern" = "target/hello-0.0.1.war",
                        "target" = "helloworld-greeting-project/${BUILD_NUMBER}/",
                        "props": "Integration-Tested=Yes;Performance-Tested=No"
                    }
                }
            }***
            server.upload(uploadSpec)
            }
        }

    }
}
