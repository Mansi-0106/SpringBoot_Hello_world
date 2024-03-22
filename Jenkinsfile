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

def waitForQualityGate() {
    def scannerHome = tool 'sonar-scanner'
    withSonarQubeEnv('SonarQube_Server_Name') {
        script {
            // Update SonarQube URL with correct scheme
            def sonarURL = "http://localhost:9000"
            if (!sonarURL.startsWith('http://') && !sonarURL.startsWith('https://')) {
                sonarURL = "http://" + sonarURL
            }
            sh '''
                ${scannerHome}/bin/sonar-scanner \
                -Dsonar.projectKey=hello-world-git \
                -Dsonar.sources=src \
                -Dsonar.host.url=${sonarURL} \
                -Dsonar.login=sqp_010d6c670e4cbe2dada76eac60239013bada5ce8
            '''
        }
    }
}
