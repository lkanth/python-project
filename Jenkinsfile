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
                    final String url = "https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711"
                    final String SMAX_AUTH_TOKEN = sh(script: "curl -d '{\"login\":\"tenantAdmin\",\"password\":\"Admin_1234\"}' -X POST https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711 -k --header \"Content-Type: application/json\"", returnStdout: true).trim()
                    echo SMAX_AUTH_TOKEN
                    final String CREATE_VM = sh(script: "curl -X POST https://catvmlmpoc1.ftc.hpeswlab.net/rest/616409711/ess/request/createRequest -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=616409711;SMAX_AUTH_TOKEN=eyJ0eXAiOiJKV1MiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIyYzkwYzQ5Zjc3ZWJlYWRkMDE3N2VlY2IzMzU4MTY1NCIsImlzcyI6IklkTSAxLjMxLjIiLCJjb20uaHBlLmlkbTp0cnVzdG9yIjpudWxsLCJleHAiOjE2MTY1NDA0NzYsImNvbS5ocC5jbG91ZDp0ZW5hbnQiOnsiaWQiOiIyYzkwYzQ5Zjc3ZWJlYWRkMDE3N2VlYzg1NWU2MTVlNyIsIm5hbWUiOiI2MTY0MDk3MTEiLCJlbmFibGVkIjp0cnVlfSwicHJuIjoidGVuYW50QWRtaW4iLCJpYXQiOjE2MTY1MzY4NzYsImp0aSI6Ijg3ZTg1YjczLWNkMmMtNDc1My1iMzk5LTc0MmI5ODk3NmQ4OSJ9.cLUhLBhtR7UYU-he82Dbf23bN47R2HRXzDUjjQTfhG0\" -d '{\"entities\":[{\"entity_type\":\"Request\",\"properties\":{\"RequestedForPerson\":\"10015\",\"StartDate\":1616452009778,\"RequestsOffering\":\"10096\",\"CreationSource\":\"CreationSourceEss\",\"RequestedByPerson\":\"10015\",\"DataDomains\":[\"Public\"],\"UserOptions\":\"{\\"complexTypeProperties\\":[{\\"properties\\":{\\"OptionSet0c6eb101a1a178c3c49c3badbc481f05_c\\":{\\"Option34c8d8d8403ac43361b8b8083004ef4a_c\\":true},\\"OptionSet2ee4a8f73fcd1606c1337172e8411e2a_c\\":{\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\":true},\\"OptionSet473C6F2BE6F45DB8381664FC9097BE37_c\\":{\\"Option2E8493EA9AC2821929DA64FC90978A98_c\\":true},\\"changedUserOptionsForSimulation\\":\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c&\\",\\"PropertyproviderId2E8493EA9AC2821929DA64FC90978A98_c\\":\\"2c908fac77eefca5017822299d726af6\\",\\"PropertydatacenterName2E8493EA9AC2821929DA64FC90978A98_c\\":\\"CAT\\",\\"PropertyvirtualMachine2E8493EA9AC2821929DA64FC90978A98_c\\":\\"catvmlmdeployvm***CentOS 4/5 or later (64-bit)\\",\\"PropertycustomizationTemplateName2E8493EA9AC2821929DA64FC90978A98_c\\":\\"(Ts)catvmLinuxDHCP\\",\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\":true,\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c\\":false}}]}\",\"Description\":\"<p>hello vm</p>\",\"RelatedSubscriptionName\":\"webserver\",\"RelatedSubscriptionDescription\":\"<p>webserver</p>\",\"RequestAttachments\":\"{\\"complexTypeProperties\\":[]}\",\"DisplayLabel\":\"Request: vCenter Compute - Deploy VM from Template\"}}],\"operation\":\"CREATE\"}'", returnStdout: true).trim()
                    echo CREATE_VM
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
