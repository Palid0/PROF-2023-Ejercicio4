pipeline {
    agent any

    stages {
        stage('Database Maintenance') {
            steps {
                script {
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
        // stage('Github Notify'){
        //    steps {
        //        script{
        //        }    
        //    }
        // }
    }

    post {
        success {
            script {
                currentBuild.result = 'SUCCESS'
                echo 'Database maintenance successful!'
            }
        }
        failure {
            script {
                currentBuild.result = 'FAILURE'
                echo 'Database maintenance failed!'
            }
        }
    }
}
