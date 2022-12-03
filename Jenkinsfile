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
                    docker.withRegistry('https://registry.hub.docker.com', 'DokerHub')
                    app.push 'latest'
                    app.push '${env.BUILD_TAG}'
                }
            }
        }
    }
}
