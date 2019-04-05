def APP_MODULE = "app"

pipeline {
    agent none

    environment {
        GRADLE_USER_HOME = '/.gradle'
        GRADLE_TEMP = '/.gradle_cache'
    }

    stages {
        stage('Checkout') {
            agent any

            steps {
                checkout scm
                stash name: 'Checkout'
            }
        }

        stage('pr-detekt') {
            agent {
                docker {
                    image 'localhost:5000/android-env'
                    args "-v ${HOME}/.gradle:${GRADLE_TEMP}"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                sh "mkdir -p $GRADLE_USER_HOME"
                unstash name: 'Checkout'

                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"
                sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_TEMP}/ ${GRADLE_USER_HOME} || true"
                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"
//                sh './gradlew clean detekt'
                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    deleteDir()
                    echo 'Detekt Success!!!'
                }

                failure {
                    deleteDir()
                    echo 'Test run failure!!!'
                }
            }
        }
    }

    post {
        success {
            deleteDir()
            echo 'build is success!!!'
        }
        failure {
            deleteDir()
            echo 'build is failure!!!'
        }
    }
}
