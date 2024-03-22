pipeline {
    agent any

    stages {
        stage('Generate Sonar Report and Package Jar File') {
            steps {
                dir("helloworld") {
                    script {
                        if (isUnix()) {
                            sh "sudo mvn clean verify sonar:sonar -Dsonar.projectKey=hello-world-git -Dsonar.projectName='hello-world-git' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_010d6c670e4cbe2dada76eac60239013bada5ce8"
                        } else {
                            bat "mvn clean verify sonar:sonar -Dsonar.projectKey=hello-world-git -Dsonar.projectName='hello-world-git' -Dsonar.host.url=http://localhost:9000 -Dsonar.token=sqp_010d6c670e4cbe2dada76eac60239013bada5ce8"
                        }
                    }
                }
            }
        }

        stage('Quality Gate Check') {
            steps {
                script {
                    // Use 'withSonarQubeEnv' wrapper around waitForQualityGate()
                    withSonarQubeEnv('SonarQube') {
                        timeout(time: 1, unit: 'HOURS') {
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
