pipeline {

    stages {
        stage('App Build') {
            agent {
                node {
                    label "jenkins-slave-mvn"
                }
            }

            steps {
                echo '💙 Deploying to artifact repository...'
                sh "mvn -B deploy scm:tag -Drevision=${env.BUILD_NUMBER}"
            }
        }

        stage('Bake') {
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

        stage('Deploy to DEV') {
        }

        stage('Deploy to TEST') {

        }

        stage('Deploy to PROD') {
            // You should be pretty sure that it works by now :-)
            // TODO: Add Manual Approval step.
        }
    }
}