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
				sh 'mkdir build'
				sh 'cp HelloWorld.sh build/HelloWorld.sh'
				sh 'chmod 555 build/HelloWorld.sh'
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
                        /*
						final String HCMX_TENANT_ID = env.HCMX_TENANT_ID
                        final String HCMX_SERVER_FQDN = env.HCMX_SERVER_FQDN
                        final String HCMX_AUTH_URL = "https://" + HCMX_SERVER_FQDN + "/auth/authentication-endpoint/authenticate/token?TENANTID=" + HCMX_TENANT_ID
                        final String SMAX_AUTH_TOKEN = sh(script: "curl -d '{\"login\":\"$USERNAME\",\"password\":\"$PASSWORD\"}' -X POST $HCMX_AUTH_URL -k --header \"Content-Type: application/json\"", returnStdout: true).trim()
                        final String HCMX_CREATE_REQUEST_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ess/request/createRequest"
                        final def (String response, int code) = sh(script: "curl -s -w '\\n%{response_code}' -X POST $HCMX_CREATE_REQUEST_URL -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\" -d '{\"entities\":[{\"entity_type\":\"Request\",\"properties\":{\"RequestedForPerson\":\"10015\",\"StartDate\":1616452009778,\"RequestsOffering\":\"10096\",\"CreationSource\":\"CreationSourceEss\",\"RequestedByPerson\":\"10015\",\"DataDomains\":[\"Public\"],\"UserOptions\":\"{\\\"complexTypeProperties\\\":[{\\\"properties\\\":{\\\"OptionSet0c6eb101a1a178c3c49c3badbc481f05_c\\\":{\\\"Option34c8d8d8403ac43361b8b8083004ef4a_c\\\":true},\\\"OptionSet2ee4a8f73fcd1606c1337172e8411e2a_c\\\":{\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true},\\\"OptionSet473C6F2BE6F45DB8381664FC9097BE37_c\\\":{\\\"Option2E8493EA9AC2821929DA64FC90978A98_c\\\":true},\\\"changedUserOptionsForSimulation\\\":\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c&\\\",\\\"PropertyproviderId2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"2c908fac77eefca5017822299d726af6\\\",\\\"PropertydatacenterName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"CAT\\\",\\\"PropertyvirtualMachine2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"catvmlmdeployvm***CentOS 4/5 or later (64-bit)\\\",\\\"PropertycustomizationTemplateName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"(Ts)catvmLinuxDHCP\\\",\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true,\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c\\\":false}}]}\",\"Description\":\"<p>hello vm</p>\",\"RelatedSubscriptionName\":\"webserver\",\"RelatedSubscriptionDescription\":\"<p>webserver</p>\",\"RequestAttachments\":\"{\\\"complexTypeProperties\\\":[]}\",\"DisplayLabel\":\"Request: vCenter Compute - Deploy VM from Template\"}}],\"operation\":\"CREATE\"}'", returnStdout: true).trim().tokenize("\n")
                        echo "HTTP response status code: $code"
                        if (code == 200) 
						{
                            def responseJSON = new groovy.json.JsonSlurperClassic().parseText(response)
                            echo response
                            def HCMX_REQUEST_ID = responseJSON.entity_result_list.entity[0].properties.Id
                            echo "HCMX REQUEST ID = $HCMX_REQUEST_ID"
                            
                            final String HCMX_GET_REQUEST_STATUS_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Request?filter=Id=" + HCMX_REQUEST_ID + "\\&layout=PhaseId"
                            println HCMX_GET_REQUEST_STATUS_URL
                            String reqStatus = "Submitted"
                            int reqCode = 0
                            String reqResponse = "Nothing"
                            while (reqStatus != 'Close')
                            {
                                 println HCMX_GET_REQUEST_STATUS_URL
                                (reqResponse, reqCode) = sh(script: "curl -s -w '\\n%{response_code}' $HCMX_GET_REQUEST_STATUS_URL -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
                                echo "HTTP response status code: $reqCode"
                                if (reqCode == 200) 
								{
                                    def reqResponseJSON = new groovy.json.JsonSlurperClassic().parseText(reqResponse)
                                    echo reqResponse
                                    reqStatus = reqResponseJSON.entities[0].properties.PhaseId
                                    echo "HCMX REQUEST status = $reqStatus"
                                    if (reqStatus.equalsIgnoreCase("Close"))
                                    {
                                        break;
                                    }
                                    else
                                    {
                                        echo "sleep for 30 seconds before checking status again"
                                        sleep(30)
                                    }                                        
                                }
                            }
                            
                            final String HCMX_GET_SUBSCRIPTION_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Subscription?filter=(InitiatedByRequest=%27" + HCMX_REQUEST_ID + "%27%20and%20Status=%27Active%27)&layout=Id"
                            println HCMX_GET_SUBSCRIPTION_URL
                            final def (String subResponse, int subRescode)  = sh(script: "curl -s -w '\\n%{response_code}' \"$HCMX_GET_SUBSCRIPTION_URL\" -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
                            if (subRescode == 200) 
                            {
								def subResponseJSON = new groovy.json.JsonSlurperClassic().parseText(subResponse)
								echo subResponse
								subID = subResponseJSON.entities[0].properties.Id
								echo "HCMX Subscription ID = $subID" 
				    
								final String HCMX_GET_SVCINSTANCE_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/cloud-service/getServiceInstance/" + subID
								println HCMX_GET_SVCINSTANCE_URL
								final def (String svcInstResponse, int svcInstRescode)  = sh(script: "curl -s -w '\\n%{response_code}' \"$HCMX_GET_SVCINSTANCE_URL\" -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
								if (svcInstRescode == 200) 
								{
									def svcInstResponseJSON = new groovy.json.JsonSlurperClassic().parseText(svcInstResponse)
									echo svcInstResponse
									def svcInstTopologyArray = svcInstResponseJSON.topology
									def ipAddress = ""

									for(def member : svcInstTopologyArray) 
									{
										if(member.type.name == 'CI_TYPE_SERVER') 
										{
											echo "this is server component"
											def svcInstPropertyArray = member.properties
											for(def propMember : svcInstPropertyArray) 
											{
												if(propMember.name == 'primary_ip_address') 
												{
													ipAddress = propMember.propertyValue
													echo "IP address is $ipAddress"													 
													break
												}
											}
											break
										}
									}
									
											
									final String subscriberID="10015"
									final String HCMX_CANCEL_SUBSCRIPTION_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ess/subscription/cancelSubscription/" + subscriberID + "/" + subID
									println HCMX_CANCEL_SUBSCRIPTION_URL
									final def (String subCancelResponse, int subCancelRescode)  = sh(script: "curl -s -w '\\n%{response_code}' -X PUT \"$HCMX_CANCEL_SUBSCRIPTION_URL\" -k --header \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
									if (subCancelRescode == 200) 
									{
										def subCancelResponseJSON = new groovy.json.JsonSlurperClassic().parseText(subCancelResponse)
										echo subCancelResponse					                                                                         
									}
									echo subCancelResponse
								}
							}              
						}
						*/
						sshagent(['HCMXDeployedVMSSHKey']) 
						{
    						def ipAddress = "16.78.123.170"
							echo "IP address is $ipAddress"	
							final String scpCMDOutput = sh(script: "scp -rp ./build root@$ipAddress:/tmp/", returnStdout: true).trim()
							println scpCMDOutput
							final String remoteCMDOutput = sh(script: "ssh root@$ipAddress /tmp/build/HelloWorld.sh", returnStdout: true).trim()
							println remoteCMDOutput
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
