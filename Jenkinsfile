pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                echo 'Running Tests...'
                sh './mvnw clean test' // Chạy các test case
            }
            post {
                always {
                    // Upload kết quả test lên Jenkins
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Code Coverage') {
            steps {
                echo 'Generating Coverage Report...'
                sh './mvnw org.jacoco:jacoco-maven-plugin:report' // Tạo báo cáo coverage với JaCoCo
                recordCoverage(tools: [[parser: 'JACOCO']]) // Upload độ phủ testcase
            }
        }

        stage('Build') {
            steps {
                echo 'Building Application...'
                sh './mvnw clean install' // Build file .jar
            }
            post {
                success {
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true // Lưu artifact
                }
            }
        }
    }
}