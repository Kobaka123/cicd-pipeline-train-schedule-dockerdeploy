pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
        stage('Build Image') {
            steps {
                script {
                    app = docker.build 'kobaka123/train-schedule'
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'DockerHub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('Deploy to Production') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'webserver-ssh', keyFileVariable: 'key', passphraseVariable: '', usernameVariable: 'user')])  {
                    script {
                        sh 'ssh deploy@52.90.0.221 -i /home/cloud_user/.ssh/id_rsa "docker pull kobaka123/train-schedule:latest; docker run --restart-always --name train-schedule -p 8080:8080 -d kobaka123/train-schedule:latest"'
                    }
                }
            }
        }
    }
}
