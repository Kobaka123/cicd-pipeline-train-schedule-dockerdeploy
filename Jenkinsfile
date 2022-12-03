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
                    app = docker.build 'Kobaka123/train-schedule'
                }
            }
        }
    }
}
