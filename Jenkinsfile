pipeline {
    agent any

    environment {
        NEXUS_TOKEN = 'a11c4160-7643-3de8-a6a0-e994f4f17cf5'
    }

    stages {
        stage('Checkout Frontend') {
            steps {
                dir('pidevopsfront') {
                    git url: 'https://github.com/ahmed123trabelsi/pidevopsfront.git', branch: 'main'
                }
            }
        }

        stage("Checkout Backend") {
            steps {
                dir('pidevopsnest') {
                    echo 'Pulling backend repository...'
                    git branch: 'main', url: 'https://github.com/ahmed123trabelsi/pidevopsnest.git'
                }
            }
            post {
                success {
                    echo "Cloning of backend successful..."
                }
                failure {
                    echo "Cloning of backend failed! See log for details. Terminating..."
                }
            }
        }
        
        stage('Install dependencies (Backend)') {
            steps {
                script {
                    dir('pidevopsnest') {
                        sh('npm install --force')
                    }
                }
            }
        }
        
        stage('Build application (Backend)') {
            steps {
                script {
                    dir('pidevopsnest') {
                        sh('npm run build')
                    }
                }
            }
        }
        
        stage('Dépôt sur Nexus') {
            steps {
                script {
                    dir('pidevopsnest') {
                        // Setup the .npmrc file using environment variable for auth
                        sh 'echo registry=http://192.168.33.10:8081/repository/npmhostednest/ > .npmrc'
                        sh 'echo //192.168.33.10:8081/repository/npmhostednest/:_authToken=${NEXUS_TOKEN} >> .npmrc'
                        
                        // Publish package to Nexus npm repository
                        sh 'npm publish --registry=http://192.168.33.10:8081/repository/npmhostednest/'
                    }
                }
            }
        }
        
        stage('Remove Previous Containers') {
            steps {
                script {
                    dir('pidevopsnest') {
                        sh 'ls -la' // List the contents of the current directory
                        sh 'docker compose down'
                        sh 'docker rmi server_app:latest --force'
                        sh 'docker rmi server_web:latest --force'
                    } 
                    echo 'removing...'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    dir('pidevopsnest') {
                        sh 'ls -la' // List the contents of the current directory
                        sh 'docker compose up -d'
                    }
                    echo 'Deploying application...'
                }
            }
        }
    }
}
