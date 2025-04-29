pipeline {
    agent any

    stages {
        stage("CODE COPY") {
            steps {
                git url: "https://github.com/NagleAniket/two-tier-flask-app", branch: "master"
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
}
