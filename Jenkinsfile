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
        stage('Semgrep Scan') {
            steps {
                sh 'mkdir -p results'
                sh 'semgrep scan --config auto . --json --output results/semgrep.json'
        
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'results/semgrep.json', fingerprint: true, allowEmptyArchive: true
            defectDojoPublisher(
                artifact: 'results/semgrep.json', 
                productName: 'Juice Shop', 
                scanType: 'Semgrep JSON Report', 
                engagementName: 'p.sorota@sonel.pl'
            )
        }
    }
}
