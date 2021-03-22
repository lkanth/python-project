pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Building..'
                def response = httpRequest "https://www.yahoo.com"
println('Status: '+response.status)
println('Response: '+response.content)
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
