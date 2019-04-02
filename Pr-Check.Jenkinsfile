pipeline {
    agent any

    stages {
        stage('pr-check') {
            agent {
                    docker { image 'android-pr-check' }
                }

            steps {
                sh './gradlew detekt'
                sh 'bundle exec danger --danger_id=android-detekt'
            }
        }
     }

    post {
        success {
            echo 'build is success!!!'
        }
        failure {
            echo 'build is failure!!!'
        }
    }
}
