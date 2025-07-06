@Library('Shared') _  // Referencing your shared library
parameters {
    string(name: 'DOCKER_TAG', defaultValue: 'v1', description: 'Setting docker image for latest push')
}

pipeline {
    agent any
    environment {
        SONAR_HOME = tool "Sonar"
        DOCKER_IMAGE = "bankapp"
        GIT_REPO = "https://github.com/anilsahu350/DevOps-mega-project.git"
        GIT_BRANCH = "feature/devsecops"
    }
    stages {
        stage("Clean Workspace") {
            steps {
                cleanWs()
            }
        }
        stage("Code Clone") {
            steps {
                script {
                    code_checkout("https://github.com/anilsahu350/DevOps-mega-project.git", "feature/devsecops")
                }
            }
        }
        stage("SonarQube Quality Analysis") {
            steps {
                sonarqube_analysis('Sonar', 'bankapp', 'bankapp')
            }
        }
        // stage("OWASP Dependency Check") {
        //     steps {
        //         owasp_dependency()
        //     }
        // }
        stage("Sonar Quality Gate Scan") {
            steps {
                sonarqube_code_quality()
            }
        }
        stage("Trivy File System Scan") {
            steps {
                trivy_scan()
            }
        }
        stage("Docker Build") {
            steps {
                docker_build("bankapp", "${params.DOCKER_TAG}", "anilsahu350")
            }
        }
        stage("Push to Docker Hub") {
            steps {
                docker_push("bankapp", "${params.DOCKER_TAG}", "anilsahu350")
            }
        }
    }
    post {
        success {
            echo "Pipeline completed successfully!"
            emailext (
                subject: "SUCCESS: Jenkins Pipeline for ${DOCKER_IMAGE}",
                body: """
                    <div style="font-family: Arial, sans-serif; padding: 20px; border: 2px solid #4CAF50; border-radius: 10px;">
                        <h2 style="color: #4CAF50;">🎉 Pipeline Execution: SUCCESS 🎉</h2>
                        <p style="font-size: 16px; color: #333;">
                            Hello Team,
                        </p>
                        <p style="font-size: 16px; color: #333;">
                            The Jenkins pipeline for <strong style="color: #4CAF50;">${DOCKER_IMAGE}</strong> completed <strong style="color: #4CAF50;">successfully</strong>!
                        </p>
                        <table style="width: 100%; border-collapse: collapse; margin-top: 20px;">
                            <tr style="background-color: #f2f2f2;">
                                <th style="text-align: left; padding: 8px; border: 1px solid #ddd;">Details</th>
                                <th style="text-align: left; padding: 8px; border: 1px solid #ddd;">Values</th>
                            </tr>
                            <tr>
                                <td style="padding: 8px; border: 1px solid #ddd;">Git Repository</td>
                                <td style="padding: 8px; border: 1px solid #ddd;">${GIT_REPO}</td>
                            </tr>
                            <tr>
                                <td style="padding: 8px; border: 1px solid #ddd;">Branch</td>
                                <td style="padding: 8px; border: 1px solid #ddd;">${GIT_BRANCH}</td>
                            </tr>
                        </table>
                        <p style="font-size: 16px; color: #333; margin-top: 20px;">
                            Visit <a href="${BUILD_URL}" style="color: #4CAF50;">Pipeline Logs</a> for more details.
                        </p>
                        <p style="font-size: 16px; color: #333; margin-top: 20px;">
                            Thanks,<br>
                            <strong>Jenkins</strong>
                        </p>
                    </div>
                """,
                to: "anilsahu350@gmail.com",
                mimeType: 'text/html',
                attachmentsPattern: '**/table-report.html'  // Will attach if file exists
            )
        }
        failure {
            echo "Pipeline failed. Please check the logs."
            emailext (
                subject: "FAILURE: Jenkins Pipeline for ${DOCKER_IMAGE}",
                body: """
                    <div style="font-family: Arial, sans-serif; padding: 20px; border: 2px solid #F44336; border-radius: 10px;">
                        <h2 style="color: #F44336;">🚨 Pipeline Execution: FAILURE 🚨</h2>
                        <p style="font-size: 16px; color: #333;">
                            Hello Team,
                        </p>
                        <p style="font-size: 16px; color: #333;">
                            Unfortunately, the Jenkins pipeline for <strong style="color: #F44336;">${DOCKER_IMAGE}</strong> has <strong style="color: #F44336;">failed</strong>.
                        </p>
                        <table style="width: 100%; border-collapse: collapse; margin-top: 20px;">
                            <tr style="background-color: #f2f2f2;">
                                <th style="text-align: left; padding: 8px; border: 1px solid #ddd;">Details</th>
                                <th style="text-align: left; padding: 8px; border: 1px solid #ddd;">Values</th>
                            </tr>
                            <tr>
                                <td style="padding: 8px; border: 1px solid #ddd;">Git Repository</td>
                                <td style="padding: 8px; border: 1px solid #ddd;">${GIT_REPO}</td>
                            </tr>
                            <tr>
                                <td style="padding: 8px; border: 1px solid #ddd;">Branch</td>
                                <td style="padding: 8px; border: 1px solid #ddd;">${GIT_BRANCH}</td>
                            </tr>
                        </table>
                        <p style="font-size: 16px; color: #333; margin-top: 20px;">
                            Visit <a href="${BUILD_URL}" style="color: #F44336;">Pipeline Logs</a> for more details.
                        </p>
                        <p style="font-size: 16px; color: #333; margin-top: 20px;">
                            Thanks,<br>
                            <strong>Jenkins</strong>
                        </p>
                    </div>
                """,
                to: "anilsahu350@gmail.com",
                mimeType: 'text/html',
                attachmentsPattern: '**/table-report.html'
            )
        }
    }
}
