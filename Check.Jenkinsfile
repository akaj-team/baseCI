def APP_MODULE = "app"
def GRADLE_VERSION = "4.10.3"
def GRADLE_WRAPPER_VERSION = "gradle-4.10.3-all"

pipeline {
    agent none

    environment {
        GRADLE_USER_HOME = '/.gradle'
        GRADLE_TEMP = '/.gradle_temp'
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
                    image "localhost:5000/android-env"
                    args "-v /Users/vinhhuynhl.b/.gradle:$GRADLE_TEMP:rw"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                sh "mkdir -p $GRADLE_USER_HOME"
                sh "chmod 777 $GRADLE_USER_HOME"
                sh "chmod 777 $GRADLE_TEMP"
                unstash name: 'Checkout'

                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"

                sh "rsync -au -v --include /caches/${GRADLE_VERSION} --include /wrapper/dist/${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/ ${GRADLE_USER_HOME} || true"
                sh "ls -la $GRADLE_USER_HOME"
//                sh './gradlew clean detekt'
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
}
