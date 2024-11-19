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
            sh 'osv-scanner --lockfile package-lock.json --format sarif --output osv_scanner-report.sarif'
        }
    }
    post {
        always {
            defectDojoPublisher(
                artifact: '${WORKSPACE}/osv_scanner-report.sarif', 
                productName: 'Juice Shop', 
                scanType: 'OSV Scanner Scan', 
                engagementName: 'p.sorota@sonel.pl'
            )
        }
    }
}
