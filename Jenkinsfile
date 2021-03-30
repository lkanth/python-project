pipeline 
{
    agent any
    environment 
	{
        // HCMX Server's fully qualified domain name
		HCMX_SERVER_FQDN = 'catvmlmpoc1.ftc.hpeswlab.net'
        
		// HCMX tenant's ID that has DND capability. DND capability is required to provision and manage VMs.
		// HCMX will be used to provision VMs on which testing of the new build will be performed.
		// After testing is complete, provisioned VMs are deleted so that expenses on public cloud is reduced and resource usage on private cloud is reduced.
		HCMX_TENANT_ID = '616409711'
		EXAMPLE_CREDS = credentials('HCMXUser')
		
    }

    stages 
	{
        stage('Build') 
		{
            steps 
			{
                /*  Build your project in this section.
					You may use maven, ant, etc to build your project.
					Compile code. Perform unit test and integration tests.
					In this example use case, a sample HelloWorld.sh script that prints "Hello World" is built.
					Neither Maven nor ant is required. The HelloWorld.sh is used directly as from source as the compiled code in this example use case.
				*/
				
				echo '-----------------------BUILDING-----------------------'
				sh 'mkdir build'
				sh 'cp HelloWorld.sh build/HelloWorld.sh'
				sh 'chmod 555 build/HelloWorld.sh'
            }
        }
        stage('Test') 
		{
            steps 
			{
                /*  Test your project in this section.
					Perform UAT testing on the test server.
					In this use case example, a new VM is provisioned using HCMX. 
					Build is transferred to the new VM. Testing of build is conducted on the new VM.
					After testing is complete, the new VM is deleted through HCMX to release resource usage on the cloud provider.
				*/
				
				echo '-----------------------TESTING-----------------------'				
				sh('curl -k -X POST https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711 -H "Content-Type: application/json" -d \'{"login": "\'"$EXAMPLE_CREDS_USR"\'", "password": "\'"$EXAMPLE_CREDS_PSW"\'"}\'')
				//sh("curl -k -X POST https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711 -H \"Content-Type: application/json\" -d '{\"login\": \"$EXAMPLE_CREDS_USR\", \"password\": \"$EXAMPLE_CREDS_PSW\"}'")
				sh('echo "This is start"')
				sh('echo "\'"$EXAMPLE_CREDS_USR $EXAMPLE_CREDS_PSW"\'"')
				sh('echo "This is end"')
				script 
				{
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'HCMXUser', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) 
					{
                        
						final int HCMX_REQ_STATUS_CHK_INTERVAL_SECONDS = 30
						final int HCMX_SUB_CANCEL_DELAY_SECONDS = 1
						
						
						final String HCMX_TENANT_ID = env.HCMX_TENANT_ID
                        final String HCMX_SERVER_FQDN = env.HCMX_SERVER_FQDN
						
						
						echo "HCMX: Get SMAX Auth Token"
						// HCMX REST APIs require SMAX AUTH TOKEN and TENANT ID to perform any POST, PUT and GET operations.
						// Build HCMX Authentication Token URL
                        final String HCMX_AUTH_URL = "https://" + HCMX_SERVER_FQDN + "/auth/authentication-endpoint/authenticate/token?TENANTID=" + HCMX_TENANT_ID
						
						// Submit a REST API call to HCMX to get SMAX_AUTH_TOKEN
						
                        //final def (String SMAX_AUTH_TOKEN, int getTokenResCode) = sh(script: "set +x;curl -s -w '\\n%{response_code}' -X POST $HCMX_AUTH_URL -k -H \"Content-Type: application/json\" -d '{\"login\":\"$USERNAME\",\"password\":\"$PASSWORD\"}' ", returnStdout: true).trim().tokenize("\n")
						
						
						final def (String SMAX_AUTH_TOKEN, int getTokenResCode) = sh(script: 'set +x;curl -s -w \'\\n%{response_code}\' -X POST "\'"$HCMX_AUTH_URL"\'" -k -H "Content-Type: application/json" -d \'{"login":"\'"$USERNAME"\'","password":"\'"$PASSWORD"\'"}\' ', returnStdout: true).trim().tokenize("\n")
						
						error 'Failed to get SMAX_AUTH_TOKEN'
						
						if (getTokenResCode == 200)
						{
						
						
							echo "HCMX: Get person ID"
							// Build HCMX Get Person ID URL
							final String HCMX_GET_PERSON_ID_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Person?filter=(Upn=%27" + USERNAME + "%27)&layout=Id"
							
							// Submit a REST API call to HCMX to get Person ID
							final def (String personIDResponse, int personIDResCode)  = sh(script: "set +x;curl -s -w '\\n%{response_code}' \"$HCMX_GET_PERSON_ID_URL\" -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
							
							if (personIDResCode == 200) 
							{
								def personIDResponseJSON = new groovy.json.JsonSlurperClassic().parseText(personIDResponse)							
								def HCMX_PERSON_ID = personIDResponseJSON.entities[0].properties.Id
								echo "HCMX Requested for person ID is $HCMX_PERSON_ID"
							
								echo "HCMX: Submit request for a new VM for testing"
								// Build HCMX create request URL
								final String HCMX_CREATE_REQUEST_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ess/request/createRequest"
								
								// Submit a REST API call to HCMX to deploy a new test server VM 
								final def (String depVMResponse, int depVMResponseCode) = sh(script: "set +x;curl -s -w '\\n%{response_code}' -X POST $HCMX_CREATE_REQUEST_URL -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\" -d '{\"entities\":[{\"entity_type\":\"Request\",\"properties\":{\"RequestedForPerson\":\"$HCMX_PERSON_ID\",\"StartDate\":1616452009778,\"RequestsOffering\":\"10096\",\"CreationSource\":\"CreationSourceEss\",\"RequestedByPerson\":\"$HCMX_PERSON_ID\",\"DataDomains\":[\"Public\"],\"UserOptions\":\"{\\\"complexTypeProperties\\\":[{\\\"properties\\\":{\\\"OptionSet0c6eb101a1a178c3c49c3badbc481f05_c\\\":{\\\"Option34c8d8d8403ac43361b8b8083004ef4a_c\\\":true},\\\"OptionSet2ee4a8f73fcd1606c1337172e8411e2a_c\\\":{\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true},\\\"OptionSet473C6F2BE6F45DB8381664FC9097BE37_c\\\":{\\\"Option2E8493EA9AC2821929DA64FC90978A98_c\\\":true},\\\"changedUserOptionsForSimulation\\\":\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c&\\\",\\\"PropertyproviderId2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"2c908fac77eefca5017822299d726af6\\\",\\\"PropertydatacenterName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"CAT\\\",\\\"PropertyvirtualMachine2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"catvmlmdep_t***CentOS 4/5 or later (64-bit)\\\",\\\"PropertycustomizationTemplateName2E8493EA9AC2821929DA64FC90978A98_c\\\":\\\"(Ts)catvmLinuxDHCP\\\",\\\"Optionfda5ee32d7d24a63cb0035926c667e8b_c\\\":true,\\\"Optionad52a8efe1465faa8c389ae92bf90d0c_c\\\":false}}]}\",\"Description\":\"<p>hello world test vm</p>\",\"RelatedSubscriptionName\":\"HelloWorldVM\",\"RelatedSubscriptionDescription\":\"<p>HelloWorldVM</p>\",\"RequestAttachments\":\"{\\\"complexTypeProperties\\\":[]}\",\"DisplayLabel\":\"Request: vCenter Compute - Deploy VM from Template\"}}],\"operation\":\"CREATE\"}'", returnStdout: true).trim().tokenize("\n")
								
								echo "Deploy a new test server VM request: HTTP response status code: $depVMResponseCode"
								
								if (depVMResponseCode == 200) 
								{
									def depVMResponseJSON = new groovy.json.JsonSlurperClassic().parseText(depVMResponse)								
									def HCMX_REQUEST_ID = depVMResponseJSON.entity_result_list.entity[0].properties.Id
									echo "HCMX Request ID to deploy a new test server VM is $HCMX_REQUEST_ID"
									
									// Build HCMX Get request status URL
									final String HCMX_GET_REQUEST_STATUS_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Request?filter=Id=" + HCMX_REQUEST_ID + "\\&layout=PhaseId"
									String reqStatus = ""
									int reqCode = 0
									String reqResponse = ""
									
									// Loop until Request status changes to Close. Once it is in closed status VM is deployed and ready for testing.
									while (reqStatus != 'Close')
									{
										// Submit a REST API call to HCMX to get status of VM deployment request
										echo "HCMX: Get request status until it is Closed"
										(reqResponse, reqCode) = sh(script: "set +x;curl -s -w '\\n%{response_code}' $HCMX_GET_REQUEST_STATUS_URL -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
																				
										if (reqCode == 200) 
										{
											def reqResponseJSON = new groovy.json.JsonSlurperClassic().parseText(reqResponse)
											reqStatus = reqResponseJSON.entities[0].properties.PhaseId
											echo "HCMX REQUEST status is $reqStatus"
											if (reqStatus.equalsIgnoreCase("Close"))
											{
												// If request for VM deployment has moved to Close phase, then VM has been deployed successfully.
												break;
											}
											else
											{
												echo "sleep for $HCMX_REQ_STATUS_CHK_INTERVAL_SECONDS seconds before checking request status again"
												sleep(HCMX_REQ_STATUS_CHK_INTERVAL_SECONDS)
											}                                        
										}
									}
									
									echo "HCMX: Get subscription ID"
									// Build HCMX Get subscription URL using the request ID that was obtained in earlier steps.
									final String HCMX_GET_SUBSCRIPTION_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ems/Subscription?filter=(InitiatedByRequest=%27" + HCMX_REQUEST_ID + "%27%20and%20Status=%27Active%27)&layout=Id"
									
									// Submit a REST API call to HCMX to get subscription ID
									final def (String subResponse, int subRescode)  = sh(script: "set +x;curl -s -w '\\n%{response_code}' \"$HCMX_GET_SUBSCRIPTION_URL\" -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
									if (subRescode == 200) 
									{
										def subResponseJSON = new groovy.json.JsonSlurperClassic().parseText(subResponse)									
										subID = subResponseJSON.entities[0].properties.Id
										echo "HCMX Subscription ID is $subID" 
										
										echo "HCMX: Get service instances"
										// Prepare HCMX Get service instance URL using the subscription ID that was obtained in earlier steps.
										final String HCMX_GET_SVCINSTANCE_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/cloud-service/getServiceInstance/" + subID
										
										// Submit a REST API call to HCMX to get a list of service instances associated with the subscription
										final def (String svcInstResponse, int svcInstRescode)  = sh(script: "set +x;curl -s -w '\\n%{response_code}' \"$HCMX_GET_SVCINSTANCE_URL\" -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
										if (svcInstRescode == 200) 
										{
											def svcInstResponseJSON = new groovy.json.JsonSlurperClassic().parseText(svcInstResponse)
											def svcInstTopologyArray = svcInstResponseJSON.topology
											def ipAddress = ""
											
											echo "HCMX: Looping through service instances to find IP address of deployed VM"
											// Loop through service instances. Retrieve IP address property value for the service instance with type = CI_TYPE_SERVER
											for(def member : svcInstTopologyArray) 
											{
												if(member.type.name == 'CI_TYPE_SERVER') 
												{
													def svcInstPropertyArray = member.properties
													for(def propMember : svcInstPropertyArray) 
													{
														if(propMember.name == 'primary_ip_address') 
														{
															ipAddress = propMember.propertyValue																										 
															break
														}
													}
													break
												}
											}
											
											echo "HCMX: IP address of deployed virtual machine is $ipAddress"	
											
											// Copy build to the deployed virtual machine for testing.
											echo "Copying build to the deployed VM for testing"
											final String scpCMDOutput = sh(script: "scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -rp ./build root@$ipAddress:/tmp/", returnStdout: true).trim()
											
											// Test build on the deployed virtual machine.
											echo "Testing build on the deployed VM"
											final String remoteCMDOutput = sh(script: "ssh -o StrictHostKeyChecking=no root@$ipAddress /tmp/build/HelloWorld.sh", returnStdout: true).trim()
											
											// For demo and testing only. Comment out this line in production environment.
											echo "sleep for $HCMX_SUB_CANCEL_DELAY_SECONDS seconds before canceling subscription to delete deployed VM for testing."
											sleep(HCMX_SUB_CANCEL_DELAY_SECONDS)
											
											echo "HCMX: Canceling subscription"
											// Cancel subscription to delete deployed virtual machines. This frees up resources on cloud provider and reduces cloud spend.
											// Prepare HCMX cancel subscription URL using the subscription ID and the person ID that were obtained in earlier steps.
											final String HCMX_CANCEL_SUBSCRIPTION_URL = "https://" + HCMX_SERVER_FQDN + "/rest/" + HCMX_TENANT_ID + "/ess/subscription/cancelSubscription/" + HCMX_PERSON_ID + "/" + subID
											
											// Submit a REST API call to HCMX to cancel subscription, thereby delete deployed VM
											final def (String subCancelResponse, int subCancelRescode)  = sh(script: "set +x;curl -s -w '\\n%{response_code}' -X PUT \"$HCMX_CANCEL_SUBSCRIPTION_URL\" -k -H \"Content-Type: application/json\" -H \"Accept: application/json\" -H \"Accept: text/plain\" --cookie \"TENANTID=$HCMX_TENANT_ID;SMAX_AUTH_TOKEN=$SMAX_AUTH_TOKEN\"", returnStdout: true).trim().tokenize("\n")
											
											
											if (subCancelRescode == 200) 
											{
												echo "HCMX Subscription canceled successfully"
											}
											else
											{
												echo "HCMX subscription cancellation failed. Removal of deployed VM has failed."
												echo "HCMX Subscription cancellation response: $subCancelResponse"
											}
											
											
											// Validate test results from build execution results on remotely deployed virtual machine
											if(remoteCMDOutput == "Hello World")
											{
												echo "Testing of new build was succesful.. Proceeding to deploy stage."
											}
											else
											{
												echo "Testing of new build has failed... "
												error 'Testing of new build has failed...'
											}								
										}
										else
										{
											echo "Failed to get service instances from HCMX"
											error 'Failed to get service instances from HCMX'
										}
									} 
									else
									{
										echo "Failed to get subscription ID from HCMX"
										error 'Failed to get subscription ID from HCMX'
									}
								}
								else
								{
									echo "Request to deploy new virtual machines has failed"
									error 'Request to deploy new virtual machines has failed'
								}
							}
							else
							{
								echo "Unable to get user ID for the user $USERNAME to submit REST API calls to HCMX... "
								error 'Unable to get user ID for the user $USERNAME to submit REST API calls to HCMX... '
							}
						}
						else
						{
							echo "Failed to get SMAX_AUTH_TOKEN"
							error 'Failed to get SMAX_AUTH_TOKEN'
						}
					}
                }
            }
        }
        stage('Deploy') 
		{
            steps 
			{
				/*  Deploy your new build to production environment.
					In this use case example, we do not deploy build to the production environment.
					This use case's main purpose is to demonstrate usage of HCMX to provision new test VMs, test new build on those test VMs and 
					delete those test VMs after testing is complete.
				*/
                
				echo '-----------------------DEPLOYING-----------------------'
            }
        }
    }
}
