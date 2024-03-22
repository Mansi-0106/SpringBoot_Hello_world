pipeline {
    agent any

    stages {
        stage('Generate Sonar Report and Package Jar File') {
            steps {
                dir("helloworld") {
                    script {
                        if (isUnix()) {
                            sh "sudo mvn clean verify sonar:sonar -Dsonar.projectKey=hello-world-git -Dsonar.projectName='hello-world-git'"
                        } else {
                            bat "mvn clean verify sonar:sonar -Dsonar.projectKey=hello-world-git -Dsonar.projectName='hello-world-git'"
                        }
                    }
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                script {
                    // Use 'withSonarQubeEnv' wrapper around SonarQube steps
                    withSonarQubeEnv('SonarQube') {
                        timeout(time: 1, unit: 'HOURS') {
                            script {
                                def qg = waitForQualityGate()
                                if (qg.status != 'OK') {
                                    error "Pipeline aborted due to Quality Gate failure: ${qg.status}"
                                } else {
                                    echo "Quality Gate passed: ${qg.status}"
                                }
                            }
                        }
                    }
                }
            }
        }
    }
}
