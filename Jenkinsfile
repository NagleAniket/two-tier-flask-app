pipeline {
    agent {label 'jenkins_slave'}

    stages {
        stage("CODE COPY") {
            steps {
                git url: "https://github.com/NagleAniket/two-tier-flask-app", branch: "master"
            }
        }
        stage("Trivy File System Scan"){
            steps{
                sh "trivy fs . -o results.json"
                sh "cat results.json"
            }
        }
        stage("PREPARE BUILD") {
            steps {
                writeFile file: '.dockerignore', text: 'mysql-data/'
            }
        }

        stage("Fix MySQL Permissions") {
            steps {
                sh 'sudo chown -R devops:devops /var/lib/mysql || true'
            }
        }

        stage("CODE TEST") {
            steps {
                echo "CODE TEST/FEEDBACK"
            }
        }

        stage("CODE DEPLOY") {
            steps {
                sh "docker build -t flask-app:latest ."
            }
        }

        stage("CODE PUSH ON DOCKER HUB") {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: "dockerHubCreds",
                        passwordVariable: "dockerHubPass",
                        usernameVariable: "dockerHubUser"
                    )
                ]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag flask-app ${env.dockerHubUser}/flask-app:latest"
                    sh "docker push ${env.dockerHubUser}/flask-app"
                }
            }
        }

        stage("APPLICATION Deploy") {
            steps {
                sh "docker compose up -d --build flask-app"
            }
        }
    }
//post{
//   success{emailext(subject: 'Build Successful', body: 'Your Build Was Completed Successful!', to: 'nagleaniket@gmail.com')}
//    failure{emailext(subject: 'Build Failed', body: 'Your Build Was Failed!', to: 'nagleaniket@gmail.com')}
//}
//    
}
