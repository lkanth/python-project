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
				sh 'cp CreateFile.sh build/CreateFile.sh'
            }
        }
        stage('Test') 
		{
            steps 
			{
                echo 'Testing..'
				script{
				
                                        @groovy.transform.Field String ipAddress = "16.78.123.170"
					echo $ipAddress
					@groovy.transform.Field String scpCMD = "scp -rp ./build root@$ipAddress:/tmp/"
					echo $scpCMD
					sh '$scpCMD'
					sh 'ssh root@$ipAddress /tmp/build/CreateFile.sh'
					sh 'if ssh root@$ipAddress stat /tmp/BuildOutput.txt \\> /dev/null 2\\>\\&1 then echo "File exists" else echo "File does not exist" fi'					
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
