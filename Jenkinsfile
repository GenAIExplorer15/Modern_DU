pipeline {
    agent any

    environment {
        UIPATH_ORCH_URL = "https://rpa.local"
        UIPATH_TENANT = "Dev1"
        UIPATH_FOLDER_DEV = "Shared"
        UIPATH_FOLDER_PROD = "RPA_Automation"
        UIPATH_CLIENT_ID = "5a544aa0-77fe-4d8e-ab96-0faa3d3ec786"
    }

    stages {

        stage('Preparing') {
            steps {
                echo "Jenkins Home ${JENKINS_HOME}"
                echo "Jenkins URL ${JENKINS_URL}"
                echo "Jenkins JOB Number ${BUILD_NUMBER}"
                echo "Jenkins JOB Name ${JOB_NAME}"
                echo "GitHub Branch ${BRANCH_NAME}"
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Package') {
            steps {
                echo "Packaging UiPath Project..."

                UiPathPack(
                    versionType: 'AutoVersion',
                    outputPath: 'output',
                    projectPath: '.'
                )
            }
        }

        stage('Test') {
            steps {
                echo "Running workflow tests..."
            }
        }

        stage('Deploy to UAT') {
            steps {

                echo "Deploying package to UAT folder..."

                UiPathDeploy(
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_TENANT}",
                    folderName: "${UIPATH_FOLDER_DEV}",
                    packagesPath: "output/*.nupkg",

                    credentials: Token(
                        accountName: "",
                        clientId: "${UIPATH_CLIENT_ID}",
                        credentialsId: "APIUserKey"
                    )
                )
            }
        }

        stage('Deploy to Production') {
            steps {

                echo "Deploying package to Production folder..."

                UiPathDeploy(
                    orchestratorAddress: "${UIPATH_ORCH_URL}",
                    orchestratorTenant: "${UIPATH_TENANT}",
                    folderName: "${UIPATH_FOLDER_PROD}",
                    packagesPath: "output/*.nupkg",

                    credentials: Token(
                        accountName: "",
                        clientId: "${UIPATH_CLIENT_ID}",
                        credentialsId: "APIUserKey"
                    )
                )
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }

        always {
            cleanWs()
        }
    }
}