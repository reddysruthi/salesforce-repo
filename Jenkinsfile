#!groovy

 

node {

 

    def SF_CONSUMER_KEY='3MVG9fe4g9fhX0E4fUVa.JhS5PvbUrVzUAXwY71jOhg9qjDjJ5zfwzFPbfA72C.YJ3nHtR5yKRRiu0WhbkZkJ'

    def SF_USERNAME='kondagari@sruthi.reddy'

    def SERVER_KEY_CREDENTIALS_ID='66a3ee6c-af85-482a-a322-e7ecd0149c0d'

    //def DEPLOYDIR='src'

    def TEST_LEVEL='RunLocalTests'

    def SF_INSTANCE_URL='https://login.salesforce.com'

 

 

    //def toolbelt = tool 'toolbelt'

 

 

    // -------------------------------------------------------------------------

    // Check out code from source control.

    // -------------------------------------------------------------------------

 

    stage('checkout source') {

        checkout scm

    }

 

 

    // -------------------------------------------------------------------------

    // Run all the enclosed stages with access to the Salesforce

    // JWT key credentials.

    // -------------------------------------------------------------------------

 

	withEnv(["HOME=${env.WORKSPACE}"]) {	


	    withCredentials([file(credentialsId: SERVER_KEY_CREDENTIALS_ID, variable: 'server_key_file')]) {

		// -------------------------------------------------------------------------

		// Authenticate to Salesforce using the server key.

		// -------------------------------------------------------------------------

 

		stage('Authorize to Salesforce') {

			rc = command "sfdx auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --jwtkeyfile ${server_key_file} --username ${SF_USERNAME} --setalias UAT"

		    if (rc != 0) {

			error 'Salesforce org authorization failed.'

		    }

		}

 

 

		// -------------------------------------------------------------------------

		// Deploy metadata and execute unit tests.

		// -------------------------------------------------------------------------

 

		stage('Deploy and Run Tests') {

		    rc = command "sfdx force:mdapi:deploy --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"

		    if (rc != 0) {

			error 'Salesforce deploy and test run failed.'

		    }

		}


