pipeline 
{
    agent any
    environment 
	{
        HCMX_SERVER_FQDN = 'catvmlmpoc1.ftc.hpeswlab.net'
        HCMX_TENANT_ID = '616409711'       
    }

    stages 
	{
        stage('Build') 
		{
            steps 
			{
                echo 'Building..'                
            }
        }
        stage('Test') 
		{
            steps 
			{
                echo 'Testing..'
                script 
				{
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'HCMXUser', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) 
					{
                        final String HCMX_TENANT_ID = env.HCMX_TENANT_ID
                        final String HCMX_SERVER_FQDN = env.HCMX_SERVER_FQDN
                        final String HCMX_AUTH_URL = "https://" + HCMX_SERVER_FQDN + "/auth/authentication-endpoint/authenticate/token?TENANTID=" + HCMX_TENANT_ID
                        final String SMAX_AUTH_TOKEN = "eyJ0eXAiOiJKV1MiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIyYzkwYzQ5Zjc3ZWJlYWRkMDE3N2VlY2IzMzU4MTY1NCIsImlzcyI6IklkTSAxLjMxLjIiLCJjb20uaHBlLmlkbTp0cnVzdG9yIjpudWxsLCJleHAiOjE2MTY2MjcxMjIsImNvbS5ocC5jbG91ZDp0ZW5hbnQiOnsiaWQiOiIyYzkwYzQ5Zjc3ZWJlYWRkMDE3N2VlYzg1NWU2MTVlNyIsIm5hbWUiOiI2MTY0MDk3MTEiLCJlbmFibGVkIjp0cnVlfSwicHJuIjoidGVuYW50QWRtaW4iLCJpYXQiOjE2MTY2MjM1MjIsImp0aSI6ImRjNjE3NDY1LTEyODQtNDc3MC1hZTc3LTZiZTNmMThmMDg5MCJ9.a_J-KlxVejaUx88K93vsEeyf4humMAkBbbC22pg5XX4"
                        final String HCMX_REQUEST_ID = "11867"                      
                            final String HCMX_GET_SUBSCRIPTION_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Subscription?filter=(InitiatedByRequest=%27" + HCMX_REQUEST_ID + "%27)\\&layout=Id"
                            println HCMX_GET_SUBSCRIPTION_URL
                            final def (String subResponse, int subRescode)  = sh(script: "curl -v -s -w '\\n%{response_code}' \"$HCMX_GET_SUBSCRIPTION_URL\" -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
		            println subResponse
			    println subRescode
                            if (subRescode == 200) 
                            {
                                    def subResponseJSON = new groovy.json.JsonSlurperClassic().parseText(subResponse)
                                    echo subResponse
                                    subID = subResponseJSON.entities[0].properties.Id
                                    echo "HCMX Subscription ID = $subID"                                                                         
                            }                                     
                    }
                }
            }
        }
        stage('Deploy') 
		{
            steps 
			{
                echo 'Deploying....'
            }
        }
    }
}
