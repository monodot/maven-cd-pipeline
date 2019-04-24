openshift.withCluster() {
    env.NAMESPACE = openshift.project()

    env.POM_FILE = env.BUILD_CONTEXT_DIR ? "${env.BUILD_CONTEXT_DIR}/pom.xml" : "pom.xml"
//    env.BUILD_REVISION = now.format("yyyyMMddHHmmss")
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
        BUILD_REVISION = new Date().format("yyyyMMddHHmmss")

        POM_FILE = getPomFilePath("${BUILD_CONTEXT_DIR}")

        ARTIFACT_ID = readMavenPom(file: "${POM_FILE}").getArtifactId()
//        BUILD_TAG = "${ARTIFACT_ID}-${BUILD_REVISION}"

/*
        env.DEPLOY_VERSION = sh (returnStdout: true, script: "docker run --rm -v '${env.WORKSPACE}':/repo:ro softonic/ci-version:0.1.0 --compatible-with package.json").trim()
        e

        GIT_SERVER_NAME=$(echo ${GIT_URL} | cut -d'/' -f3)
        GIT_PROJECT_NAME=$(echo ${GIT_URL} | cut -d'/' -f4)
        GIT_REPO_NAME=$(echo ${GIT_URL} | cut -d'/' -f5)

        git remote remove origin
        git remote add origin https://${GIT_CREDENTIALS_USR}:${GIT_CREDENTIALS_PSW}@${GIT_SERVER_NAME}/${GIT_PROJECT_NAME}/${GIT_REPO_NAME}
*/

    }

    stages {

        stage('App Build') {

            steps {
                // Declarative pipeline automatically performs a checkout of source code,
                // and if the sourceSecret attribute is set in the OpenShift BuildConfig,
                // then the repo will be cloned using the credentials in the Secret.


                // TODO We could write code here to instead get the highest numbered tag, and increment it by 1

                echo 'Tagging this commit with the build revision'

                sh "git config --global user.email jenkins@example.com"
                sh "git config --global user.name jenkins"
                sh 'git tag -fa ${BUILD_TAG} -m "CI build revision ${BUILD_REVISION}"'

                sh 'git push origin ${BUILD_TAG}'

/*
                // Create a tag at the HEAD revision
                // To do this, we need to add credentials so that Jenkins can push tags
                withCredentials([usernamePassword(credentialsId: '${GIT_CREDENTIAL_ID}',
                        passwordVariable: 'GIT_PASSWORD',
                        usernameVariable: 'GIT_USERNAME')]) {
                    sh 'git push origin ${BUILD_TAG}'
                }
*/

                echo '💙 Deploying to artifact repository...'
                echo '😉 -DskipTests=true, for all the true developers'
                sh "mvn -B deploy scm:tag -DskipTests=true -Drevision=${BUILD_REVISION} -f ${POM_FILE}"
            }
        }

        stage('Bake') {
            steps {
                echo 'Download artifact from repository'
/*
                sh '''
                    mvn dependency:get -DartifactId=${POM_ARTIFACT_ID} -Dversion=${POM_VERSION} -Dtransitive=false
                    mvn dependency:copy -Dartifact=${POM_GROUP_ID}:${POM_ARTIFACT_ID}:${POM_VERSION} -DoutputDirectory=.
                    mv ${POM_ARTIFACT_ID}-${POM_VERSION}.jar application.jar
                '''

                echo 'Create container image from binary'
                sh '''
                    oc patch bc ${APP_NAME} -p "{\\"spec\\":{\\"output\\":{\\"to\\":{\\"kind\\":\\"ImageStreamTag\\",\\"name\\":\\"${APP_NAME}:${JENKINS_TAG}\\"}}}}" -n ${BUILD_NAMESPACE}
                    oc start-build ${APP_NAME} --from-file=application.jar --follow -n ${BUILD_NAMESPACE}
                '''
*/
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