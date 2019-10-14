def GRADLE_VERSION = "4.10.3"
def GRADLE_WRAPPER_VERSION = "gradle-4.10.3-all"
def DEPLOY_ENV = "Debug"
pipeline {
    agent none

    environment {
        GRADLE_USER_HOME = '/.gradle'
        GRADLE_TEMP = '/.gradle_temp'
        // Authenticate using service account
        // https://firebase.google.com/docs/app-distribution/android/distribute-gradle
        FIREBASE_TOKEN = '1//0eVsn13Pwk83HCgYIARAAGA4SNwF-L9IrEpWBs6XEITDKDZqylKe1J6t5r2hKA2RJ5QNy56bp0zjiesjZD8gUVkRwBM3Y4Ei0keg'
    }

    stages {
        stage('Checkout') {
            agent {
                label 'master'
            }

            steps {
                stash name: 'Source-Code'
            }
        }

        stage('Deploy Development') {
            agent {
                docker {
                    label "master"
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
                unstash name: 'Source-Code'

                // https://unix.stackexchange.com/questions/67539/how-to-rsync-only-new-files
                // -t & --ignore-existing
                sh "rsync -r -au --include /${GRADLE_VERSION} --exclude '/*' ${GRADLE_TEMP}/caches/ ${GRADLE_USER_HOME}/caches || true"
                sh "rsync -r -au --include /${GRADLE_WRAPPER_VERSION} --exclude '/*' ${GRADLE_TEMP}/wrapper/dists/ ${GRADLE_USER_HOME}/wrapper/dists || true"

                writeFile file: "release_notes.txt", text: "${BRANCH_NAME}: #${BUILD_ID}\n"
                sh 'git log --oneline -1 >> release_notes.txt'
                sh "./gradlew --stacktrace assemble${DEPLOY_ENV} appDistributionUpload${DEPLOY_ENV}"

                sh "rsync -au ${GRADLE_USER_HOME}/caches ${GRADLE_USER_HOME}/wrapper ${GRADLE_TEMP}/ || true"
            }

            post {
                always {
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
