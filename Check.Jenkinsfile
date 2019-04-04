def APP_MODULE = "app"

pipeline {
    agent {
        docker {
            image 'localhost:5000/android-env'
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
                sh 'ENV GRADLE_USER_HOME /.gradle'
                sh "mkdir -p ${env.GRADLE_USER_HOME}"
                sh 'ENV GRADLE_USER_CACHE /.gradle_cache'
                sh "mkdir -p ${env.GRADLE_USER_CACHE}"

                sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${env.GRADLE_USER_CACHE} / ${env.GRADLE_USER_HOME}"
                sh './gradlew clean detekt'
                sh "rsync -au ${env.GRADLE_USER_HOME}/caches ${env.GRADLE_USER_HOME}/wrapper ${env.GRADLE_USER_CACHE}/"
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    echo 'Detekt Success!!!'
                    deleteDir()
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
            echo 'build is success!!!'
        }
        failure {
            echo 'build is failure!!!'
        }
    }
}
