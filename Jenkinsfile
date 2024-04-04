pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                label 'node1'
            }
            steps {
                echo 'Building the application'
                sh '/opt/apache-maven-3.9.6/bin/mvn clean package'
            }
        }
        stage('Test') {
            agent {
                label 'node1'
            }
            steps {
                echo 'Running tests'
                sh 'mvn test'
                stash name: 'jenkins-CL-CD', includes: "target/*.war" // Corrected the closing parenthesis here
            }
        }
        stage('Deploy') {
            agent {
                label 'node2'
            }
            steps {
                echo 'Deploying the application'
                unstash 'jenkins-CL-CD'
                sh "sudo rm -rf ~/apache*/webapps/*.war"
                sh "sudo mv target/*.war ~/apache*/webapps/"
                sh "sudo systemctl daemon-reload"
                sh "sudo ~/apache-tomcat-7.0.94/bin/shutdown.sh && sudo ~/apache-tomcat-7.0.94/bin/startup.sh"
            }
        }
    }
    post {
        always {
            // Send email notification on completion
            emailext (
                body: "Check console output at $BUILD_URL to see results,",           
                subject: "Jenkins Build ${currentBuild.currentResult} jenkins-CL-CD",
                to: "yakubulatifah28@gmail.com, ibrahimlife999@gmail.com", 
                mimeType: 'text/html'
            )
        }
    }
}
