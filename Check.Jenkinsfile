def APP_MODULE = "app"
pipeline {
    agent {
         docker {
            image 'localhost:5000/android-env'
            args '-v /Users/vinhhuynhl.b/.gradle:.gradle:rw'
         }
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
    }

    stages {
        stage('pr-detekt') {
            steps {
                sh './gradlew clean detekt'
            }

            post {
                success {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                    echo 'Detekt Success!!!'
                }
            }
        }

        stage('pr-unit-test') {
            steps {
                sh './gradlew clean test jacoco'
            }

            post {
                always {
                     echo 'Report unit test to jenkins!'
                     junit '**/test-results/**/*.xml'

                     echo 'Archive artifact'
                     archiveArtifacts artifacts: 'app/build/reports/**'
                }
                success {
                     stash includes: "${APP_MODULE}/build/reports/jacoco/jacocoTestReport/jacocoTestReport.xml", name: 'jacoco-test-report'
                     echo 'Test run success!!!'
                }
                failure {
                     echo 'Test run failure!!!'
                }
            }
        }
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
