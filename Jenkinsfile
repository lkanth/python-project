pipeline {
    agent any
    environment {
        HCMX_SERVER_FQDN = 'catvmlmpoc1.ftc.hpeswlab.net'
        HCMX_TENANT_ID = '616409711'       
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building..'                
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
                script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'HCMXUser', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                        final String HCMX_TENANT_ID = env.HCMX_TENANT_ID
                        final String HCMX_SERVER_FQDN = env.HCMX_SERVER_FQDN
                        final String HCMX_AUTH_URL = "https://" + HCMX_SERVER_FQDN + "/auth/authentication-endpoint/authenticate/token?TENANTID=" + HCMX_TENANT_ID
                        final String SMAX_AUTH_TOKEN = sh(script: "curl -d '{\"login\":\"$USERNAME\",\"password\":\"$PASSWORD\"}' -X POST $HCMX_AUTH_URL -k --header \"Content-Type: application/json\"", returnStdout: true).trim()
                        final String HCMX_CREATE_REQUEST_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ess/request/createRequest"
                        final def (String response, int code) = sh(script: "curl -s -w '\\n%{response_code}' -X POST $HCMX_CREATE_REQUEST_URL -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\" -d '{\"entities\":[{\"entity_type\":\"Request\",\"properties\":{\"RequestedForPerson\":\"10015\",\"StartDate\":1616452009778,\"RequestsOffering\":\"10096\",\"CreationSource\":\"CreationSourceEss\",\"RequestedByPerson\":\"10015\",\"DataDomains\":[\"Public\"],\"UserOptions\":\"{\\\"complexTypeProperties\\\":[{\\\"properties\\\":{\\\"OptionSet0c6eb101a1a178c3c49c3badbc481f05_c\\\":{\\\"Option34c8d8d8403ac43361b8b8083004ef4a_c\\\":true},\\\"OptionSet2ee4a8f73fcd1606c1337172e8411e2a_c\\\":{\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true},\\\"OptionSet473C6F2BE6F45DB8381664FC9097BE37_c\\\":{\\\"Option2E8493EA9AC2821929DA64FC90978A98_c\\\":true},\\\"changedUserOptionsForSimulation\\\":\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c&\\\",\\\"PropertyproviderId2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"2c908fac77eefca5017822299d726af6\\\",\\\"PropertydatacenterName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"CAT\\\",\\\"PropertyvirtualMachine2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"catvmlmdeployvm***CentOS 4/5 or later (64-bit)\\\",\\\"PropertycustomizationTemplateName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"(Ts)catvmLinuxDHCP\\\",\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true,\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c\\\":false}}]}\",\"Description\":\"<p>hello vm</p>\",\"RelatedSubscriptionName\":\"webserver\",\"RelatedSubscriptionDescription\":\"<p>webserver</p>\",\"RequestAttachments\":\"{\\\"complexTypeProperties\\\":[]}\",\"DisplayLabel\":\"Request: vCenter Compute - Deploy VM from Template\"}}],\"operation\":\"CREATE\"}'", returnStdout: true).trim().tokenize("\n")
                        echo "HTTP response status code: $code"
                        if (code == 200) {
                            def json = new groovy.json.JsonSlurperClassic().parseText(response)
                            def HCMX_REQUEST_ID = json.actions.find { it._class == "entity_result_list.entity.properties" }.Id
                            echo "HCMX REQUEST ID = $HCMX_REQUEST_ID"
                        }                  
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
