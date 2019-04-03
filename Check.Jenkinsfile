def APP_MODULE = "app"

pipeline {
    agent {
        docker {
            image 'localhost:5000/android-env'
            args '-v /Users/vinhhuynhl.b/.gradle:/.gradle:rw'
        }
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
                unstash name: 'Checkout'
                sh 'mkdir -p ~/.gradle'
                sh "ENV $GRADLE_USER_HOME ~/.gradle"
                sh "mkdir -p $GRADLE_USER_HOME"
                sh 'mkdir -p ~/.gradle_cache'
                sh "ENV $GRADLE_USER_CACHE ~/.gradle_cache"
                sh "mkdir -p $GRADLE_USER_CACHE"
                sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_USER_CACHE}/ ${GRADLE_USER_HOME}"
                sh './gradlew clean detekt'
                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_USER_CACHE}/"
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    echo 'Detekt Success!!!'
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
