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
                    def response = httpRequest ignoreSslErrors: true, httpMode: 'GET', url: "http://catvmlmhcm7.ftc.hpeswlab.net:8080/"
                    println('Status: '+response.status)
                    println('Response: '+response.content)
                    //def get = new URL("https://httpbin.org/get").openConnection();
                    //def getRC = get.getResponseCode();
                    //println(getRC);
                    //if(getRC.equals(200)) {
                        //println(get.getInputStream().getText());
                    //}
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
