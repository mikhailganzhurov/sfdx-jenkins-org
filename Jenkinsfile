import groovy.json.JsonSlurperClassic
node {

	println 'KEY IS'
	println env.SF_CONSUMER_KEY
	println env.SF_USERNAME
	println env.SERVER_KEY_CREDENTIALS_ID
	println env.SF_INSTANCE_URL

    def toolbelt = tool 'toolbelt'


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
	
	    withCredentials([file(credentialsId: env.SERVER_KEY_CREDENTIALS_ID, variable: 'jwt_key_file')]) {
			// -------------------------------------------------------------------------
			// Authenticate to Salesforce using the server key.
			// -------------------------------------------------------------------------

			stage('Authorize to Salesforce') {
				rc = command "sfdx auth:jwt:grant --instanceurl ${env.SF_INSTANCE_URL} --clientid ${env.SF_CONSUMER_KEY} --jwtkeyfile C:\\Users\\442Lenovo\\SSLCert\\server.key --username ${env.SF_USERNAME} --setalias DevOrg --loglevel debug"
				if (rc != 0) {
				error 'Salesforce org authorization failed.'
				}
			}


			// -------------------------------------------------------------------------
			// Deploy metadata and execute unit tests.
			// -------------------------------------------------------------------------

			stage('Deploy and Run Tests') {
				println 'Deploy and Run Tests start'
				rc = command "${toolbelt}/sfdx force:mdapi:deploy --wait 10 --deploydir ${DEPLOYDIR} --targetusername DevOrg --testlevel ${TEST_LEVEL}"
				if (rc != 0) {
				error 'Salesforce deploy and test run failed.'
				}
				println 'Deploy and Run Tests finish'
			}


			// -------------------------------------------------------------------------
			// Example shows how to run a check-only deploy.
			// -------------------------------------------------------------------------

			//stage('Check Only Deploy') {
			//    rc = command "${toolbelt}/sfdx force:mdapi:deploy --checkonly --wait 10 --deploydir ${DEPLOYDIR} --targetusername UAT --testlevel ${TEST_LEVEL}"
			//    if (rc != 0) {
			//        error 'Salesforce deploy failed.'
			//    }
			//}
	    }
	}
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
		return bat(returnStatus: true, script: script);
    }
}
