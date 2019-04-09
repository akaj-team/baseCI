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
                stash name: 'Source-Code'
            }
        }
        stage('Parallel UT & Detekt Stage') {
            parallel {
                stage('detekt-report') {
                    agent {
                        docker {
                            image "localhost:5000/android-env"
                            // Users/vinhhuynhl.b/Desktop/Gradle-Jenkins-Local can be replace by any dir or empty dir
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
                        unstash name: 'Source-Code'

                        sh "ls -a $GRADLE_USER_HOME"
                        sh "ls -a $GRADLE_TEMP"

                        // https://unix.stackexchange.com/questions/67539/how-to-rsync-only-new-files
                        // -P & --ignore-existing
                        sh "rsync -P -au --ignore-existing --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                        sh "rsync -P -au --ignore-existing --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                        sh "ls -a $GRADLE_USER_HOME"

                        sh './gradlew clean detekt'

                        sh "rsync -P -au --ignore-existing ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
                    }

                    post {
                        success {
                            stash includes: "${APP_MODULE}/build/reports/detekt/detekt-checkstyle.xml", name: 'detekt-checkstyle'
                            echo 'Detekt Success!!!'
                        }

                        failure {
                            echo 'Detekt failure!!!'
                        }
                    }
                }

                stage('ut-report') {
                    agent {
                        docker {
                            image "localhost:5000/android-env"
                            // Users/vinhhuynhl.b/Desktop/Gradle-Jenkins-Local can be replace by any dir or empty dir
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
                        unstash name: 'Source-Code'

                        sh "ls -a $GRADLE_USER_HOME"
                        sh "ls -a $GRADLE_TEMP"

                        // https://unix.stackexchange.com/questions/67539/how-to-rsync-only-new-files
                        // -P & --ignore-existing
                        sh "rsync -P -au --ignore-existing --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                        sh "rsync -P -au --ignore-existing --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                        sh "ls -a $GRADLE_USER_HOME"

                        sh './gradlew clean test jacoco'

                        sh "rsync -P -au --ignore-existing ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
                    }

                    post {
                        always {
                            echo 'Report unit test to jenkins!!!'
                            junit '**/test-results/**/*.xml'

                            echo 'Archive artifact'
                            archiveArtifacts artifacts: 'app/build/reports/'
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
        }
    }
}
