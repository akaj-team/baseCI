def APP_MODULE = "App"
pipeline {
    agent {
         docker { image 'localhost:5000/android-env' }
    }

    stages {
        stage('pr-detekt') {
            steps {
                sh 'rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_USER_CACHE}/ ${GRADLE_USER_HOME}'
                sh './gradlew clean detekt'
                sh 'rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_USER_CACHE}/'
            }

            post {
                always {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                }

                success {
                    echo 'Detekt Success!!!'
                }
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
