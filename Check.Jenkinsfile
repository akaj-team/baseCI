def APP_MODULE = "app"
def GRADLE_VERSION = "4.10.3"

pipeline {
    agent {
        docker {
            image 'localhost:5000/android-env'
            args "-v /.gradle:/gradle_cache"
        }
    }

    environment {
        GRADLE_USER_HOME = '/.gradle'
        GRADLE_USER_CACHE = '/.gradle_cache'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                stash name: 'Checkout'
            }
        }

        stage('pr-detekt') {
            options {
                skipDefaultCheckout()
            }

            steps {
                sh "mkdir -p $GRADLE_USER_HOME"
                sh "ls -a ~$GRADLE_USER_HOME"
                unstash name: 'Checkout'
                sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_USER_CACHE} / ${GRADLE_USER_HOME} || true"
                sh './gradlew clean detekt'
                sh "rsync -au ${GRADLE_USER_HOME}/daemon/${GRADLE_VERSION} ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_USER_CACHE} / || true"
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
