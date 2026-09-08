pipeline {
    agent any

    environment {
        REPORT_DIR = 'reports'
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Build / Preparation') {
            steps {
                bat '''
                    if not exist %REPORT_DIR% mkdir %REPORT_DIR%
                    echo "Dossier des rapports pret."
                '''
            }
        }

        stage('Security Analysis') {
            steps {
                bat '''
                    npm audit --json > %REPORT_DIR%/npm-audit-report.json || exit 0
                '''
            }
        }

        stage('Additional Security Check') {
            steps {
                bat '''
                    gitleaks detect --source . --report-path %REPORT_DIR%/gitleaks-report.json --no-git || exit 0
                '''
            }
        }

        stage('Report Generation') {
            steps {
                archiveArtifacts artifacts: "${REPORT_DIR}/*", allowEmptyArchive: true, fingerprint: true
            }
        }

        stage('Notification') {
            steps {
                echo "Pipeline DevSecOps termine avec succes."
            }
        }
    }
}
