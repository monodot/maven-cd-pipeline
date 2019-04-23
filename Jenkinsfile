openshift.withCluster() {
    env.NAMESPACE = openshift.project()
    env.POM_FILE = env.BUILD_CONTEXT_DIR ? "${env.BUILD_CONTEXT_DIR}/pom.xml" : "pom.xml"

}


pipeline {

    agent {
        label 'maven'
    }

    stages {
        stage('App Build') {

            steps {
                echo '💙 Deploying to artifact repository...'
                sh "mvn -B deploy scm:tag -Drevision=${env.BUILD_NUMBER} -f ${env.POM_FILE}"
            }
        }

        stage('Bake') {
            steps {
                echo 'Download artifact from repository'
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