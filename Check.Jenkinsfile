def APP_MODULE = "app"
def DIR_NAME = "${currentBuild.startTimeInMillis}"
pipeline {
    agent {
        docker {
            image 'localhost:5000/android-env'
            args '-v /Users/vinhhuynhl.b/.gradle:/.gradle:rw'
        }
    }

    environment {
        JENKINS_NODE_COOKIE = 'dontKillMe'
    }

    stages {
        stage('Checkout') {
            steps {
                sh "mkdir -p $DIR_NAME"
                sh "chmod -R 777 /$DIR_NAME"
                checkout scm
                stash name: 'Checkout'
            }
        }

//        stage('pr-detekt') {
//            options {
//                skipDefaultCheckout()
//            }
//
//            steps {
//                unstash name: 'Checkout'
//                sh './gradlew clean detekt --parallel'
//            }
//
//            post {
//                success {
//                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
//                    echo 'Detekt Success!!!'
//                }
//            }
//        }

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
//                }
//                failure {
//                    echo 'Test run failure!!!'
//                }
//            }
//        }
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
