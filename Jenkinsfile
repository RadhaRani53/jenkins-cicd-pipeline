pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/RadhaRani53/simple-java-maven-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Deploy') {
            steps {
                sh 'cp target/*.jar /opt/tomcat9/webapps/ || cp target/*.war /opt/tomcat9/webapps/ || true'
            }
        }

    }
}
