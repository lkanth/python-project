pipeline {
    agent any

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
                    def response = httpRequest ignoreSslErrors: true, httpMode: 'POST', contentType: 'APPLICATION_JSON', requestBody: '{"login":"tenantAdmin","password":"Admin_1234"}', url: "https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711"
                    println('Status: '+response.status)
                    println('Response: '+response.content)
                    def smax_auth_token = response.content
                    def CreateVMResponse = httpRequest customHeaders: [[name: 'Cookie', value: "SMAX_AUTH_TOKEN=$smax_auth_token"]], ignoreSslErrors: true, httpMode: 'POST', contentType: 'APPLICATION_JSON', requestBody: '{"entities":[{"entity_type":"Request","properties":{"RequestedForPerson":"10015","RequestsOffering":"10096","CreationSource":"CreationSourceEss","RequestedByPerson":"10015","DataDomains":["Public"],"UserOptions":"{\"complexTypeProperties\":[{\"properties\":{\"OptionSet0c6eb101a1a178c3c49c3badbc481f05_c\":{\"Option34c8d8d8403ac43361b8b8083004ef4a_c\":true},\"OptionSet2ee4a8f73fcd1606c1337172e8411e2a_c\":{\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\":true},\"OptionSet473C6F2BE6F45DB8381664FC9097BE37_c\":{\"Option2E8493EA9AC2821929DA64FC90978A98_c\":true},\"changedUserOptionsForSimulation\":\"Optionad52a8efe1465faa8c389ae92bf90d0c_c&\",\"PropertyproviderId2E8493EA9AC2821929DA64FC90978A98_c\":\"2c908fac77eefca5017822299d726af6\",\"PropertydatacenterName2E8493EA9AC2821929DA64FC90978A98_c\":\"CAT\",\"PropertyvirtualMachine2E8493EA9AC2821929DA64FC90978A98_c\":\"catvmlmdeployvm***CentOS 4/5 or later (64-bit)\",\"PropertycustomizationTemplateName2E8493EA9AC2821929DA64FC90978A98_c\":\"(Ts)catvmLinuxDHCP\",\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\":true,\"Optionad52a8efe1465faa8c389ae92bf90d0c_c\":false}}]}","Description":"<p>need vm</p>","RelatedSubscriptionName":"webserver","RelatedSubscriptionDescription":"<p>webserver</p>","RequestAttachments":"{\"complexTypeProperties\":[]}","DisplayLabel":"Request: vCenter Compute - Deploy VM from Template"}}],"operation":"CREATE"}', url: "https://catvmlmpoc1.ftc.hpeswlab.net/rest/616409711/ess/request/createRequest"
                    println('Status: '+CreateVMResponse.status)
                    println('Response: '+CreateVMResponse.content)
                    //def get = new URL("https://httpbin.org/get").openConnection();
                    //def getRC = get.getResponseCode();
                    //println(getRC);
                    //if(getRC.equals(200)) {
                        //println(get.getInputStream().getText());
                    //}
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
