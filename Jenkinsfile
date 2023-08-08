pipeline {
    agent any
    
    stages {
        stage('Deploy MySQL') {
            steps {
                script {
                    def mysqlManifest = readFile('mysql.yaml')
                    sh "kubectl apply -f - <<< '''${mysqlManifest}'''"
                }
            }
        }
        
        stage('Deploy WordPress') {
            steps {
                script {
                    def wpManifest = readFile('wordpress.yaml')
                    sh "kubectl apply -f - <<< '''${wpManifest}'''"
                }
            }
        }
    }
    
    post {
        always {
            // Cleanup resources if needed
            sh "kubectl delete deployment mysql"
            sh "kubectl delete service mysql"
            sh "kubectl delete deployment wordpress"
            sh "kubectl delete service wp-http"
        }
    }
}
