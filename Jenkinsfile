openshift.withCluster() {
    env.NAMESPACE = openshift.project()

    env.POM_FILE = env.BUILD_CONTEXT_DIR ? "${env.BUILD_CONTEXT_DIR}/pom.xml" : "pom.xml"
//    env.BUILD_REVISION = now.format("yyyyMMddHHmmss")
}

pipeline {

    agent {
        label 'maven'
    }

    environment {
        BUILD_REVISION = new Date().format("yyyyMMddHHmmss")

        if (BUILD_CONTEXT_DIR != '') {
            POM_FILE = "${BUILD_CONTEXT_DIR}/pom.xml"
        } else {
            POM_FILE = "pom.xml"
        }

//        POM_FILE = env.BUILD_CONTEXT_DIR ? "${env.BUILD_CONTEXT_DIR}/pom.xml" : "pom.xml"
        ARTIFACT_ID = readMavenPom(file: "${POM_FILE}").getArtifactId()
        BUILD_TAG = "${ARTIFACT_ID}-${BUILD_REVISION}"
    }

    stages {
        stage('App Build') {

            steps {
/*                script {
                    pom = readMavenPom file: "${POM_FILE}"
//                    env.POM_GROUP_ID = pom.groupId
//                    env.POM_VERSION = pom.version
//                    env.POM_ARTIFACT_ID = pom.artifactId
//
                    buildTag = "${pom.artifactId}-${buildRevision}"
                }*/

                echo '💙 Deploying to artifact repository...'
                echo '😉 -DskipTests=true for all the true developers out there'

                // TODO a git tag here
                sh '''
                    git tag ${BUILD_TAG}
                    git push origin ${BUILD_TAG}
                '''
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