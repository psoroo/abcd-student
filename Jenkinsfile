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
        stage('ZAP Scan') {
            steps {
                sh 'mkdir -p results/'
                sh '''
                    docker run --name juice-shop -d --rm \
                        -p 3000:3000 \
                        bkimminich/juice-shop
                    sleep 5
                '''
                sh '''
                docker run --name zap \
                    --add-host=host.docker.internal:host-gateway \
                    -v /home/rest2t/Pulpit/abcdevsecops/reports:/zap/wrk/reports \
                    -v /home/rest2t/Pulpit/abcdevsecops/scans:/zap/wrk/:rw \
                    -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                    "zap.sh -cmd -addonupdate && \
                    zap.sh -cmd -addoninstall communityScripts && \
                    zap.sh -cmd -addoninstall pscanrulesAlpha && \
                    zap.sh -cmd -addoninstall pscanrulesBeta && \
                    zap.sh -cmd -autorun /zap/wrk/passive_scan.yaml"
                '''
            }
        }
    }
    post {
        always {
            sh 'docker container rm -f juice-shop'
            sh 'docker container rm -f zap'
            defectDojoPublisher(
                artifact: '/home/rest2t/Pulpit/abcdevsecops/reports/zap_xml_report.xml', 
                productName: 'Juice Shop', 
                scanType: 'ZAP Scan', 
                engagementName: 'p.sorota@sonel.pl'
            )
        }
    }
}
