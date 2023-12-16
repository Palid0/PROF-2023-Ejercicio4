pipeline {
    agent any

    stages {
        stage('Create the Webhook') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'TOKEN_UPM', variable: 'TOKEN_EJ4')]) {
                        def existingWebhook = sh(
                            script: 'curl -s -H "Authorization: token $TOKEN_EJ4" https://api.github.com/repos/GRISE-UPM/PROF-2023-Ejercicio4/hooks',
                            returnStdout: true).trim()
                        def URL = "http://" + sh(script: 'curl -s ifconfig.me', returnStdout: true).trim() + ":8080/ghprbhook/"
                        // Check if the webhook exists
                        if (!existingWebhook.contains("$URL")) {
                            def payload = [
                                name: 'web',
                                active: true,
                                events: ['pull_request', 'status'],
                                config: [
                                    url: URL,
                                    content_type: 'json',
                                ]
                            ]
                            def jsonPayload = groovy.json.JsonOutput.toJson(payload)
                            sh """
                                curl -X POST \
                                -H "Authorization: token $TOKEN_EJ4" \
                                -H "Accept: application/vnd.github.v3+json" \
                                -d '${jsonPayload}' \
                                https://api.github.com/repos/GRISE-UPM/PROF-2023-Ejercicio4/hooks
                            """
                        }
                        else { echo 'Skipped! Webhook already exists...'}
                    }
                }
            }
        }
        
        stage('Updating the database') {
            steps {
                script {
                    // Fetch data
                    sh 'wget -O Employees.db https://github.com/Palid0/PROF-2023-Ejercicio4/blob/main/Employees.db'

                    // Data backup
                    sh 'sqlite3 Employees.db ".dump" > backup.sql'

                    // Update Data
                    sh 'rm Employees.db'
                    sh 'sqlite3 Employees.db < sqlite.sql'

                    // Restore previous data
                    sh 'sqlite3 Employees.db < backup.sql'
                    
                }
            }
        }
    }

   post {
        success {
            script {
                echo 'test. success'
                // generate request
                if (env.ghprbActualCommit != null) {
                    echo 'msg_profesor'
                    def status = [
                            state: 'success',
                            description: 'GRISE-UPM > Managed to build pull request',
                            context: 'Jenkins'
                        ]
                    def jsonPayload = groovy.json.JsonOutput.toJson(status)
                    def RequestSHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    withCredentials([string(credentialsId: 'TOKEN_UPM', variable: 'TOKEN_EJ4')]) {
                        sh """
                        curl -X POST \
                        -H "Authorization: token $TOKEN_EJ4" \
                        -H "Accept: application/vnd.github.v3+json" \
                        -d '${jsonPayload}' \
                        https://api.github.com/repos/GRISE-UPM/PROF-2023-Ejercicio4/statuses/${pullRequestSHA}
                        """
                    }
                    
                } else {
                    echo 'msg_Palid0'
                    def status = [
                            state: 'success',
                            description: 'Palid0 > Successfully maintained database',
                            context: 'Jenkins'
                        ]
                    def jsonPayload = groovy.json.JsonOutput.toJson(status)
                    def RequestSHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    withCredentials([string(credentialsId: 'Eric', variable: 'TOKEN_EJ4')]) {
                        sh """
                        curl -X POST \
                        -H "Authorization: token $TOKEN_EJ4" \
                        -H "Accept: application/vnd.github.v3+json" \
                        -d '${jsonPayload}' \
                        https://api.github.com/repos/Palid0/PROF-2023-Ejercicio4/statuses/${RequestSHA}
                        """
                    }
                }
            }
        }
        failure {
            script {
                echo 'test. failure'
                // generate request
                if (env.CHANGE_ID != null) {
                    echo 'fallo_profesor'
                    def status = [
                            state: 'failure',
                            description: 'GRISE-UPM > Pull request could not be built',
                            context: 'Jenkins'
                        ]
                    def jsonPayload = groovy.json.JsonOutput.toJson(status)
                    def RequestSHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    withCredentials([string(credentialsId: 'TOKEN_UPM', variable: 'TOKEN_EJ4')]) {
                        sh """
                        curl -X POST \
                        -H "Authorization: token $TOKEN_EJ4" \
                        -H "Accept: application/vnd.github.v3+json" \
                        -d '${jsonPayload}' \
                        https://api.github.com/repos/GRISE-UPM/PROF-2023-Ejercicio4/statuses/${pullRequestSHA}
                        """
                    }
                } else {
                    echo 'fallo_Palid0'
                    def status = [
                            state: 'failure',
                            description: 'Palid0 > Could not update database',
                            context: 'Jenkins'
                        ]
                    def jsonPayload = groovy.json.JsonOutput.toJson(status)
                    def RequestSHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                    withCredentials([string(credentialsId: 'Eric', variable: 'TOKEN_EJ4')]) {
                        sh """
                        curl -X POST \
                        -H "Authorization: token $TOKEN_EJ4" \
                        -H "Accept: application/vnd.github.v3+json" \
                        -d '${jsonPayload}' \
                        https://api.github.com/repos/Palid0/PROF-2023-Ejercicio4/statuses/${RequestSHA}
                        """
                    }
                }
            }
        }
    }
}
