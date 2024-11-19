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
        stage('SCV Scan') {
            steps {
                sh 'mkdir -p results'
                sh 'osv-scanner --lockfile package-lock.json --format sarif --output results/osv_scanner-report.sarif || true'
        
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'results/osv_scanner-report.sarif', fingerprint: true, allowEmptyArchive: true
            defectDojoPublisher(
                artifact: 'results/osv_scanner-report.sarif', 
                productName: 'Juice Shop', 
                scanType: 'OSV Scan', 
                engagementName: 'p.sorota@sonel.pl'
            )
        }
    }
}
