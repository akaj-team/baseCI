def APP_MODULE = "app"

pipeline {
    agent any

    environment {
        GRADLE_USER_HOME = "$HOME/.gradle"
        GRADLE_USER_CACHE = "$HOME/.gradle_cache"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                stash name: 'Checkout'
            }
        }

        stage('pr-detekt') {

            agent {
                docker {
                    image 'localhost:5000/android-env'
                    args "-v /.gradle:${GRADLE_USER_CACHE}"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                sh "mkdir -p $GRADLE_USER_HOME"
                unstash name: 'Checkout'

                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_USER_CACHE"
                sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_USER_CACHE}/ ${GRADLE_USER_HOME} || true"
                sh "ls -a $GRADLE_USER_HOME"
                sh "ls -a $GRADLE_USER_CACHE"
                sh './gradlew clean detekt'
                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_USER_CACHE}/ || true"
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

//        stage('pr-unit-test') {
//            agent {
//                docker {
//                    image 'localhost:5000/android-env'
//                    args "-v /.gradle:${GRADLE_USER_CACHE}"
//                }
//            }
//
//            options {
//                skipDefaultCheckout()
//            }
//
//            steps {
//                unstash name: 'Checkout'
//                sh './gradlew clean test jacoco'
//            }
//
//            post {
//                always {
//                    echo 'Report unit test to jenkins!'
//                    junit '**/test-results/**/*.xml'
//
//                    echo 'Archive artifact'
//                    archiveArtifacts artifacts: 'app/build/reports/**'
//                }
//                success {
//                    stash includes: "${APP_MODULE}/build/reports/jacoco/jacocoTestReport/jacocoTestReport.xml", name: 'jacoco-test-report'
//                    echo 'Test run success!!!'
//                    deleteDir()
//                }
//                failure {
//                    echo 'Test run failure!!!'
//                }
//            }
//        }
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
