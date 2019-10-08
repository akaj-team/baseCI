def APP_MODULE = "app"
def GRADLE_VERSION = "5.4.1"
def GRADLE_WRAPPER_VERSION = "gradle-5.4.1-all"
def RUNNING_NODE = "macos" // nodes master || macos are available

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

        stage('detekt-report') {
            agent {
                docker {
//                    -v /var/run/docker.sock:/var/run/docker.sock
                    image "at/android-env:1.0.2"
                    label "$RUNNING_NODE"
                    args "-v gradle-data:$GRADLE_TEMP:rw --privileged"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
//                sh "sudo mkdir -p /.android"
                unstash('Source-Code')

                sh "touch $GRADLE_USER_HOME/gradle.properties"
                sh "echo 'org.gradle.daemon=true' >> $GRADLE_USER_HOME/gradle.properties"
                sh "echo 'org.gradle.configureondemand=true' >> $GRADLE_USER_HOME/gradle.properties"

                sh "rsync -r -au --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                sh "rsync -r -au --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                sh './gradlew clean detekt'

                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
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

        stage('report-github') {
            agent {
                docker {
                    label "$RUNNING_NODE"
                    image "at/reporting:latest"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                unstash('detekt-checkstyle')
                sh "bundle install --path /vendor/bundle"
            }

            post {
                success {
                    sh "bundle exec danger --danger_id=check_style --dangerfile=Dangerfile"
                    echo 'Posted To Github!!!'
                }

                failure {
                    echo 'Failed To Post Github!!!'
                }
            }
        }

        stage('ut-report') {
            agent {
                docker {
                    label "$RUNNING_NODE"
                    image "at/android-env:1.0.2"
                    args "-v gradle-data:$GRADLE_TEMP:rw -v /var/run/docker.sock:/var/run/docker.sock --privileged"
                }
            }

            options {
                skipDefaultCheckout()
            }

            steps {
                sh "touch $GRADLE_USER_HOME/gradle.properties"
                sh "echo 'org.gradle.daemon=true' >> $GRADLE_USER_HOME/gradle.properties"
                sh "echo 'org.gradle.configureondemand=true' >> $GRADLE_USER_HOME/gradle.properties"
                unstash('Source-Code')

                // https://unix.stackexchange.com/questions/67539/how-to-rsync-only-new-files
                // -t & --ignore-existing
                sh "rsync -r -au --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                sh "rsync -r -au --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                sh './gradlew clean test jacocoTestReport'

                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
            }

            post {
                always {
                    echo 'Report unit test to jenkins!!!'
                    junit '**/test-results/**/*.xml'

                    echo 'Archive artifact'
                    archiveArtifacts artifacts: 'app/build/reports/**'

                    echo 'Delete Workspace.'
                    deleteDir()
                }
                success {
                    echo 'build is success!!!'
                }
                failure {
                    echo 'build is failure!!!'
                }
            }
        }
    }
}
