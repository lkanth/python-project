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
                    final String CREATE_VM = sh(script: "/tmp/createVM.sh", returnStdout: true).trim()
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
