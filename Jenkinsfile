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
                    def response = httpRequest ignoreSslErrors: true, url: "https://www.yahoo.com"
                    println('Status: '+response.status)
                    println('Response: '+response.content)
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
