pipeline {
    agent any

    stages {
        stage('Locate changed service') {
            steps {
                script {
                    // Lấy danh sách các file đã thay đổi (đường dẫn tương đối)
                    env.CHANGED_FILES = sh(returnStdout: true, script: 'git diff --name-only HEAD^ HEAD').trim()
                    // Xác định service nào cần build
                    env.SERVICE_TO_BUILD = determineService(env.CHANGED_FILES)
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Running Tests...'
                sh './mvnw clean test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Code Coverage') {
            steps {
                echo 'Generating Coverage Report...'
                sh './mvnw org.jacoco:jacoco-maven-plugin:report'
                recordCoverage(tools: [[parser: 'JACOCO']])
            }
        }

        stage('Build') {
            steps {
                echo 'Building Application...'
                sh './mvnw clean install'
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }
    }

def determineService(changedFiles) {
    println "Changed Files: ${changedFiles}"
    def services = [
        'spring-petclinic-customers-service',
        'spring-petclinic-vets-service',
        'spring-petclinic-visits-service',
        'spring-petclinic-api-gateway',
        'spring-petclinic-admin-server' // Bao gồm tất cả các service của bạn
    ]
    def serviceToBuild = null

    for (service in services) {
        if (changedFiles.contains(service)) {
           if(serviceToBuild == null){
             serviceToBuild = service;
           } else {
             // Xử lí trường hợp có thay đổi ở nhiều service
             return "multiple"
           }

        }
    }
    return serviceToBuild
}
}