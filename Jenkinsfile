pipeline {
    agent any

    environment {
        CHANGED_DIRS = ""
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/user/spring-petclinic-microservices.git'
            }
        }

        stage('Detect Changes') {
            steps {
                script {
                    // Lấy danh sách thư mục bị thay đổi (chỉ lấy cấp 1 - service)
                    CHANGED_DIRS = sh(script: "git diff --name-only HEAD~1 HEAD | cut -d'/' -f1 | sort -u", returnStdout: true).trim()
                    echo "Changed directories: ${CHANGED_DIRS}"
                }
            }
        }

        stage('Build & Test') {
            steps {
                script {
                    def services = ['spring-petclinic-vets-service', 
                                    'spring-petclinic-customers-service', 
                                    'spring-petclinic-visits-service']

                    for (service in services) {
                        if (CHANGED_DIRS.contains(service)) {
                            echo "Building and Testing ${service}"
                            dir(service) {
                                sh './mvnw test'
                                sh './mvnw clean package -DskipTests'
                            }
                        }
                    }
                }
            }
        }
    }
}

