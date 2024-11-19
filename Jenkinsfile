pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }
    stages {
        stage('Code checkout from GitHub') {
            steps {
                script {
                    cleanWs()
                    git credentialsId: 'github-pat', url: 'https://github.com/psoroo/abcd-student', branch: 'main'
                }
            }
        }
        stage('Secret Scan') {
            steps {
                sh 'mkdir -p results'
                sh 'trufflehog git file://. -j > results/trufflehog.json'
        
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'results/trufflehog.json', fingerprint: true, allowEmptyArchive: true
            defectDojoPublisher(
                artifact: 'results/trufflehog.json', 
                productName: 'Juice Shop', 
                scanType: 'Trufflehog Scan', 
                engagementName: 'p.sorota@sonel.pl'
            )
        }
    }
}
