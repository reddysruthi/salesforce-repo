import groovy.json.JsonSlurperClassic

node {
    def BUILD_NUMBER = env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG = env.HUB_ORG_DH
    def SFDC_HOST = env.SFDC_HOST_DH
    def JWT_KEY_CRED_ID = env.JWT_CRED_ID_DH
    def CONNECTED_APP_CONSUMER_KEY = env.CONNECTED_APP_CONSUMER_KEY_DH

    println 'KEY IS'
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY

    // Define the tool name as configured in "Global Tool Configuration"
    def toolbelt = tool name: 'toolbelt', type: 'Tool'

    stage('checkout source') {
        steps {
            checkout scm
        }
        // When running in a multi-branch job, one must issue this command
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploy Code') {
            steps {
                script {
                    def rc
                    if (isUnix()) {
                        rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${CONNECTED_APP_CONSUMER_KEY} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                    } else {
                        rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${CONNECTED_APP_CONSUMER_KEY}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
                    }
                    if (rc != 0) {
                        error 'Hub org authorization failed'
                    }

                    println rc

                    // Need to pull out assigned username
                    def rmsg
                    if (isUnix()) {
                        rmsg = sh returnStdout: true, script: "${toolbelt} force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
                    } else {
                        rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:mdapi:deploy -d manifest/. -u ${HUB_ORG}"
                    }

                    println('Hello from a Job DSL script!')
                    println(rmsg)
                }
            }
        }
    }
}
