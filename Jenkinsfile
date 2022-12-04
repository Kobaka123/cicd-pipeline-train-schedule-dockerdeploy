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
                script {
                    sh "sudo ssh -tt deploy@34.205.55.110 -i /var/lib/jenkins/.ssh/id_rsa"
                    }
                
            }
        }
    }
}
