pipeline {
    agent any
    environment {
        SERVICE_DIR = getChangedService()
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Detect Changes') {
            steps {
                script {
                    SERVICE_DIR = getChangedService()
                    if (SERVICE_DIR == "") {
                        echo "No relevant changes detected. Skipping build."
                        currentBuild.result = 'SUCCESS'
                        return
                    }
                    echo "Building service: ${SERVICE_DIR}"
                }
            }
        }
        stage('Test') {
            when {
                expression { SERVICE_DIR != "" }
            }
            steps {
                script {
                    dir("${SERVICE_DIR}") {
                        sh './mvnw test'
                        junit 'target/surefire-reports/*.xml'
                        jacoco(execPattern: 'target/jacoco.exec')
                    }
                }
            }
        }
        stage('Build') {
            when {
                expression { SERVICE_DIR != "" }
            }
            steps {
                script {
                    dir("${SERVICE_DIR}") {
                        sh './mvnw package'
                    }
                }
            }
        }
    }
}


def getChangedService() {
    def changedFiles = sh(script: "git diff --name-only HEAD~1", returnStdout: true).trim().split("\n")
    def services = [
        "spring-petclinic-admin-server",
        "spring-petclinic-api-gateway",
        "spring-petclinic-config-server",
        "spring-petclinic-customers-service",
        "spring-petclinic-discovery-server",
        "spring-petclinic-genai-service",
        "spring-petclinic-vets-service",
        "spring-petclinic-visits-service"
    ]
    for (file in changedFiles) {
        for (service in services) {
            if (file.startsWith(service + "/")) {
                return service
            }
        }
    }
    return ""
}
