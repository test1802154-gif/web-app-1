pipeline {
    agent any

    environment {
        DOCKER_USER  = 'shaffat01'
        APP_NAME     = 'web-app-1'
        NOTIFY_EMAIL = 'your-email@gmail.com' // ⚠️ তোমার জিমেইল আইডি দাও
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo '📥 Pulling code from GitHub...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo '🐳 Building Docker Image...'
                sh "docker build -t ${DOCKER_USER}/${APP_NAME}:${env.BUILD_NUMBER} ."
                
                echo '🧪 Running Tests...'
                sh "docker run --rm ${DOCKER_USER}/${APP_NAME}:${env.BUILD_NUMBER} pytest --version"
            }
        }

        stage('Deploy Application') {
            steps {
                echo '🚀 Deploying Application...'
                sh "docker run -d --name ${APP_NAME}-test -p 8085:5000 ${DOCKER_USER}/${APP_NAME}:${env.BUILD_NUMBER} || true"
            }
        }
    }

    // 📧 PROFESSIONAL HTML EMAIL NOTIFICATION BLOCK
    post {
        success {
            script {
                sendProfessionalEmail(
                    "SUCCESS", 
                    "#22c55e", 
                    "🎉 Pipeline Build Passed!", 
                    "All stages executed successfully and application is live."
                )
            }
        }
        
        failure {
            script {
                sendProfessionalEmail(
                    "FAILED", 
                    "#ef4444", 
                    "🔴 Pipeline Build Failed!", 
                    "An error occurred during pipeline execution. Please check the logs."
                )
            }
        }
        
        always {
            sh 'docker rm -f ${APP_NAME}-test || true'
            sh 'docker image prune -f || true'
        }
    }
}

// 🎨 Helper Function for HTML Email Design
def sendProfessionalEmail(String status, String statusColor, String title, String description) {
    def branchName = env.BRANCH_NAME ?: 'main'
    
    emailext (
        to: "${env.NOTIFY_EMAIL}",
        subject: "[${status}] ${env.JOB_NAME} - Build #${env.BUILD_NUMBER} (${branchName})",
        mimeType: 'text/html',
        body: """
        <!DOCTYPE html>
        <html>
        <head>
            <style>
                body { font-family: 'Segoe UI', Helvetica, Arial, sans-serif; background-color: #f1f5f9; margin: 0; padding: 20px; }
                .card { max-width: 600px; background-color: #ffffff; margin: 0 auto; border-radius: 12px; overflow: hidden; box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1); border: 1px solid #e2e8f0; }
                .header { background-color: ${statusColor}; color: #ffffff; padding: 24px; text-align: center; }
                .header h1 { margin: 0; font-size: 22px; font-weight: 700; }
                .content { padding: 28px; color: #334155; }
                .status-badge { display: inline-block; background-color: ${statusColor}; color: white; padding: 6px 16px; border-radius: 20px; font-size: 13px; font-weight: bold; margin-bottom: 15px; }
                .info-table { width: 100%; border-collapse: collapse; margin-top: 15px; }
                .info-table td { padding: 10px 12px; border-bottom: 1px solid #f1f5f9; font-size: 14px; }
                .info-table td.label { font-weight: 600; color: #64748b; width: 35%; }
                .info-table td.value { font-weight: 500; color: #0f172a; }
                .btn { display: inline-block; background-color: #0f172a; color: #ffffff !important; padding: 12px 24px; text-decoration: none; border-radius: 8px; font-weight: 600; font-size: 14px; margin-top: 25px; text-align: center; }
                .footer { background-color: #f8fafc; padding: 16px; text-align: center; font-size: 12px; color: #94a3b8; border-top: 1px solid #e2e8f0; }
            </style>
        </head>
        <body>
            <div class="card">
                <div class="header">
                    <h1>${title}</h1>
                </div>
                <div class="content">
                    <span class="status-badge">${status}</span>
                    <p style="font-size: 15px; margin-top: 5px;">${description}</p>
                    
                    <table class="info-table">
                        <tr>
                            <td class="label">Project Name</td>
                            <td class="value">${env.JOB_NAME}</td>
                        </tr>
                        <tr>
                            <td class="label">Build Number</td>
                            <td class="value">#${env.BUILD_NUMBER}</td>
                        </tr>
                        <tr>
                            <td class="label">Git Branch</td>
                            <td class="value"><code>${branchName}</code></td>
                        </tr>
                        <tr>
                            <td class="label">Executed By</td>
                            <td class="value">Jenkins CI/CD Automation</td>
                        </tr>
                    </table>

                    <div style="text-align: center;">
                        <a href="${env.BUILD_URL}" class="btn">View Build Details in Jenkins ➔</a>
                    </div>
                </div>
                <div class="footer">
                    This is an automated notification from Enterprise Jenkins Server.<br>
                    Please do not reply directly to this email.
                </div>
            </div>
        </body>
        </html>
        """
    )
}
