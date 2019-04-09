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

        stage('Gradle') {
            agent any

            steps {
                dir("$GRADLE_USER_HOME") {
                    stash name: 'gradle-home'
                }
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
                dir("$GRADLE_USER_HOME") {
                    unstash 'gradle-home'
                }
                sh "ls -a $GRADLE_USER_HOME"
//                sh './gradlew clean detekt'

//                stash includes: "$GRADLE_USER_HOME", name: 'gradle-home'
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    deleteDir()
                    echo 'Detekt Success!!!'
                }

                failure {
                    deleteDir()
                    echo 'Detekt run failure!!!'
                }
            }
        }

//        stage('ut-report') {
//            agent {
//                docker {
//                    image "localhost:5000/android-env"
//                    args "-v /Users/vinhhuynhl.b/.gradle:$GRADLE_TEMP:rw"
//                }
//            }
//
//            options {
//                skipDefaultCheckout()
//            }
//
//            steps {
//                sh "mkdir -p $GRADLE_USER_HOME"
//                sh "chmod 777 $GRADLE_USER_HOME"
//                sh "chmod 777 $GRADLE_TEMP"
//                unstash name: 'Checkout'
//
//                dir("$GRADLE_USER_HOME") {
//                    unstash 'gradle-home'
//                }
//
//                sh './gradlew clean test jacoco'
//            }
//
//            post {
//                always {
//                    echo 'Report unit test to jenkins!!!'
//                    junit '**/test-results/**/*.xml'
//
//                    echo 'Archive artifact'
//                    archiveArtifacts "artifacts: ${APP_MODULE}/build/reports/**"
//
//                    deleteDir()
//
//                }
//
//                success {
//                    stash includes: "${APP_MODULE}/build/reports/jacoco/jacocoTestReport/jacocoTestReport.xml", name: 'jacoco-report'
//                    echo 'Test Success!!!'
//                }
//            }
//        }
    }
}
