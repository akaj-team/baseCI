def APP_MODULE = "App"
pipeline {
    agent {
         docker { image 'localhost:5000/android-env' }
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
    }

    stages {
        stage('pr-detekt') {
            steps {
                #sh "rsync -a --include /caches --include /wrapper --exclude '/*' ${GRADLE_USER_CACHE}/ ${GRADLE_USER_HOME}"
                sh './gradlew clean detekt'
                #sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_USER_CACHE}/"
            }

            post {
                always {
                    stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                }

                success {
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
                     stash includes: "${APP_MODULE}/build/reports/jacoco/jacocoTestReport/jacocoTestReport.xml", name: 'jacoco-test-report'
                }
                success {
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
