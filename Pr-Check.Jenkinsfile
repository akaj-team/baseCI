pipeline {
    agent any
    stages {
        agent docker {
            android-pr-check
        }

        stage('pr-check') {
            steps {
                sh './gradlew detekt'
                sh 'bundle exec danger --danger_id=android-detekt'
            }
        }
     }

    post {
        always {
            echo 'Archive artifact'
            archiveArtifacts artifacts: 'app/build/reports/**'

            echo 'Delete Workspace.'
            sh 'sudo chown -R jenkins.jenkins .'
            sh 'chmod -R +w .'
            sh 'ls -la'
            deleteDir()

        }
        success {
            echo 'build is success!!!'
        }
        failure {
            echo 'build is failure!!!'
        }
    }
}
