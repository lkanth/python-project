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
				script
				{
				
                    final String ipAddress = "16.78.123.170"
					println ipAddress
					final String scpCMD = "scp -rp ./build root@$ipAddress:/tmp/"
					echo "scpCMD is $scpCMD"
					sh '$scpCMD'
					final String runBuildCMD = "ssh root@$ipAddress /tmp/build/CreateFile.sh"
					sh '$runBuildCMD'
					final String testCMD = "if ssh root@$ipAddress stat /tmp/BuildOutput.txt \\> /dev/null 2\\>\\&1 then echo \"File exists\" else echo \"File does not exist\" fi"
					sh '$testCMD'					
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
