#!groovy

import groovy.json.JsonSlurperClassic

node {
    def BUILD_NUMBER = env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR = "tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG = 'kondagari@sruthi.reddy'
    def SFDC_HOST = 'https://login.salesforce.com'
    def JWT_KEY_CRED_ID = '66a3ee6c-af85-482a-a322-e7ecd0149c0d'
    def CONNECTED_APP_CONSUMER_KEY = '3MVG9fe4g9fhX0E4fUVa.JhS5PvbUrVzUAXwY71jOhg9qjDjJ5zfwzFPbfA72C.YJ3nHtR5yKRRiu0WhbkZkJ'

    println 'KEY IS'
    println JWT_KEY_CRED_ID
    println HUB_ORG
    println SFDC_HOST
    println CONNECTED_APP_CONSUMER_KEY

    stage('checkout source') {
        // when running in a multi-branch job, one must issue this command
        checkout scm
    }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Deploy Code') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            } else {
                rc = bat returnStatus: true, script: "sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { error 'Hub org authorization failed' }

            echo rc

            // Need to pull out assigned username
            if (isUnix()) {
                rmsg = sh returnStdout: true, script: "sfdx force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
            } else {
                rmsg = bat returnStdout: true, script: "sfdx force:source:deploy --manifest manifest/package.xml -u ${HUB_ORG}"
            }

            echo rmsg

            echo('Hello from a Job DSL script!')
            echo(rmsg)
        }
    }
}
