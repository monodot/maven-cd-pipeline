openshift.withCluster() {
    env.NAMESPACE = openshift.project()

    env.POM_FILE = env.BUILD_CONTEXT_DIR ? "${env.BUILD_CONTEXT_DIR}/pom.xml" : "pom.xml"
}

def getPomFilePath(buildContextDir) {
    if (buildContextDir == '') {
        return "pom.xml"
    } else {
        return "${buildContextDir}/pom.xml"
    }
}

pipeline {

    agent {
        label 'maven'
    }

    environment {
        POM_FILE = getPomFilePath("${BUILD_CONTEXT_DIR}")
        GROUP_ID = readMavenPom(file: "${POM_FILE}").getGroupId()
        ARTIFACT_ID = readMavenPom(file: "${POM_FILE}").getArtifactId()

        BUILD_REVISION = new Date().format("yyyyMMddHHmmss")
        GIT_BUILD_TAG = "${ARTIFACT_ID}-${BUILD_REVISION}"
    }

    stages {

        stage('App Build') {

            steps {
                // Declarative pipeline automatically performs a checkout of source code,
                // and if the sourceSecret attribute is set in the OpenShift BuildConfig,
                // then the repo will be cloned using the credentials in the Secret.

                // Create a custom credential helper that will return the username/password
                // to the repository, so we don't have to fiddle with the repo URL
                sh 'git config --local credential.helper "!p() { echo username=\\$GIT_USERNAME; echo password=\\$GIT_PASSWORD; }; p"'
                sh 'git config --global user.email jenkins@example.com'
                sh 'git config --global user.name jenkins'

                // TODO We could write code here to instead get the highest numbered tag, and increment it by 1

                echo 'Tagging this commit with the build revision'

                sh 'git tag -fa ${GIT_BUILD_TAG} -m "CI build revision ${BUILD_REVISION}"'

                // Create a tag at the HEAD revision
                // To do this, we need to add credentials so that Jenkins can push tags
                withCredentials([usernamePassword(credentialsId: GIT_CREDENTIAL_ID,
                        usernameVariable: 'GIT_USERNAME',
                        passwordVariable: 'GIT_PASSWORD')]) {
                    sh 'git push origin ${GIT_BUILD_TAG}'
                }

                echo '💙 Deploying to artifact repository...'
                echo '😉 -DskipTests=true, for all the true developers'
                sh "mvn -B deploy -DskipTests=true -Drevision=${BUILD_REVISION} -f ${POM_FILE}"
            }
        }

        stage('Bake') {
            steps {
                echo 'Download artifact from repository'

                // Single quotes turns off string interpolation
                sh 'mvn -B dependency:get -DgroupId=${GROUP_ID} -DartifactId=${ARTIFACT_ID} -Dversion=${BUILD_REVISION} -Dtransitive=false'
                sh 'mvn -B dependency:copy -Dartifact=${GROUP_ID}:${ARTIFACT_ID}:${BUILD_REVISION} -DoutputDirectory=.'
                sh 'mv ${ARTIFACT_ID}-${BUILD_REVISION}.jar application.jar'

                openshift.withCluster() {
                    openshift.withCredentials() {
                        openshift.withProject() {
                            // Update the output image tag of the BuildConfig to the new build revision
                            openshift.patch("bc/${APPLICATION_NAME}",
                                    "{ \"spec\": { \"output\": { \"to\": { \"name\": \"${APPLICATION_NAME}:${BUILD_REVISION}\"}}}}"
                            )

                            // Start the build, streaming in the binary JAR file
                            def buildConfig = openshift.selector("bc", "${APPLICATION_NAME}")
                            def build = buildConfig.startBuild('--from-file=application.jar')
                            build.logs('-f')
                        }
                    }
                }

//                sh 'oc start-build ${APPLICATION_NAME} --from-file=application.jar --follow -n ${BUILD_NAMESPACE}'
            }
        }

        stage('Deploy to DEV') {
            steps {
                echo 'TODO'
            }
        }

        stage('Deploy to TEST') {
            steps {
                echo 'TODO'
            }
        }

        stage('Deploy to PROD') {
            steps {
                echo 'TODO'
                // You should be pretty sure that it works by now :-)
                // TODO: Add Manual Approval step.
            }
        }
    }
}