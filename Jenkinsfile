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
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-user-key', keyFileVariable: 'key', passphraseVariable: '', usernameVariable: 'user')]) {
                    script {
                        sh "ssh -o StrictHostKeyChecking=no '$user'@34.205.55.110 -i '$key' \"docker pull kobaka123/train-schedule:latest\""
                        try {
                            sh "ssh -o StrictHostKeyChecking=no '$user'@34.205.55.110 -i '$key' \"docker stop train-schedule\""
                            sh "ssh -o StrictHostKeyChecking=no '$user'@34.205.55.110 -i '$key' \"docker rm train-schedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "ssh -o StrictHostKeyChecking=no '$user'@34.205.55.110 -i '$key' \"docker run --restart always --name train-schedule -p 8080:8080 -d kobaka123/train-schedule:\""
                    }  
                }              
            }
        }
    }
}
