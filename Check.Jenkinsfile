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
                    // Users/vinhhuynhl.b/Desktop/Gradle-Jenkins-Local can
                    args "-v /Users/vinhhuynhl.b/Desktop/Gradle-Jenkins-Local:$GRADLE_TEMP:rw"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                sh "mkdir -p $GRADLE_USER_HOME"
                sh "mkdir -p $GRADLE_USER_HOME/caches"
                sh "mkdir -p $GRADLE_USER_HOME/wrapper/dists"

                sh "chmod 777 $GRADLE_USER_HOME"
                sh "chmod 777 $GRADLE_USER_HOME/caches"
                sh "chmod 777 $GRADLE_USER_HOME/wrapper/dists"
                unstash name: 'Checkout'

                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"

                sh "rsync -au -v --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                sh "rsync -au -v --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                sh './gradlew clean detekt'

                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    echo 'Detekt Success!!!'
                }

                failure {
                    echo 'Test run failure!!!'
                }
            }
        }
    }
}
