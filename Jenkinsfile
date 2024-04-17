pipeline{
    agent any
    tools{
        maven "maven"
    }

    stages{
        stage ("stage:1 git clone the repo"){
            steps {
                sh "echo cloning the  repository..."
                //change to your git syntax you generated
                git branch: 'main', url: 'https://github.com/michaelkedey/web-app.git' 
            }
        }

        stage("stage :2 build the application"){
            steps{
                sh "echo building the application"
                sh "mvn clean package"
            }
        }

        stage("stage: 3 test the application"){
            steps{
                sh "echo testing the application"
                sh "mvn test"
            }
        }

        stage("stage: 4 sonarqube scan"){
            environment{ 
                ScannerHome = tool "sonarqube" 
            }
            steps{
                script{
                    withSonarQubeEnv("sonarqube") //must match the soanrqube name configured in jenkins tools
                    {
                        sh "${ScannerHome}/bin/sonar-scanner -Dsonar.projectKey=web-app" //projectkey is the name you'll to the project when deployed to sonarqube
                    }
                }
            }
        }

        stage("stage: 5 Nexus upload"){
            steps{
                // change to your nexus generated syntax
                nexusArtifactUploader artifacts: [[artifactId: 'maven-web-application', classifier: '', file: '/var/lib/jenkins/workspace/pipeline/target/web-app.war', type: 'war']], credentialsId: 'nexus-jenkins', groupId: 'com.mt', nexusUrl: '54.89.137.49:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'web-app-release', version: '3.1.2-RELEASE'
            }
        }

        stage("stage: 6 deploy to tomcat"){
            steps{
                deploy adapters: [tomcat9(credentialsId: 'tomcat-jenkins', path: '', url: 'http://54.209.33.17:8080/')], contextPath: null, war: 'target/*.war'
            }
        }

    }

}