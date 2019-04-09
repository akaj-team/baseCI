def APP_MODULE = "app"

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
                sh "mkdir -p $HOME$GRADLE_USER_HOME"
                sh "chmod 777 $GRADLE_USER_HOME"
                sh "chmod 777 $GRADLE_TEMP"
                unstash name: 'Checkout'

                sh "ls -a $HOME$GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"

                sh "rsync -au -v --include /caches/4.10.3 --include /wrapper/dist/gradle-4.10.3-all --exclude '/*' ${GRADLE_TEMP}/ $HOME${GRADLE_USER_HOME} || true"
                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"
                sh './gradlew clean detekt'
                sh "rsync -au -v $HOME${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"

                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_TEMP"
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
