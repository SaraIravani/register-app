pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/SaraIravani/register-app'
                }
        }
        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }
       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
        stage('SonarQube Analysis') {
            steps {
                script {
                     def customWorkspace = "/home/ubuntu/workspace/register-app-ci"
                     dir(customWorkspace) {
                         echo "Creating workspace directory..."
                         sh "mkdir -p ${customWorkspace}"
                     }
                     sleep 5 // Introduce a 5-second delay
                     ws(customWorkspace) {
                         withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                             echo "Running SonarQube analysis..."
                             sh "mvn sonar:sonar"
                             echo "SonarQube analysis completed."
                }
            }
        }
    }
}

	stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                }	
            }

        }    

     }
}
