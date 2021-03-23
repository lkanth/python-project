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
                    final String response = sh(script: "curl -d '{"login":"tenantAdmin","password":"Admin_1234"}' -X POST https://catvmlmpoc1.ftc.hpeswlab.net/auth/authentication-endpoint/authenticate/token?TENANTID=616409711 -k --header "Content-Type: application/json" -i", returnStdout: true).trim()
                    echo response
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
