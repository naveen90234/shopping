pipeline {
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
        Version="${env.BUILD_ID}"
    }
    stages {
        stage('Pull Source') {
            steps {
                cleanWs()
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/jeetu844/Shopping-reactJS-DevOps.git'
            }
        }
        // stage('Sonar Scan'){
        //     steps{
        //         script{
        //             withSonarQubeEnv('sonar-server') {
        //                 sh '${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=shopping'
        //             }
        //         }
        //     }
        // }
        // stage('Quality Gate'){
        //     steps{
        //         waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
        //     }
        // }
        // stage('NPM Installation'){
        //     steps{
        //         sh 'npm install'
        //     }
        // }
        // stage('Trivy FS Scan'){
        //     steps{
        //         sh 'trivy fs . > trivyfs.txt'
        //     }
        // }
        // stage('OWASP Scan'){
        //     steps{
        //         dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
        //         dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        //     }
        // }
        stage('Docker Image'){
            steps{
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
                    sh '''
                        docker build -t shopping:$Version .
                        docker tag shopping:$Version jeetu844/shopping:$Version
                        docker tag shopping:$Version jeetu844/shopping:latest
                        docker push jeetu844/shopping:$Version
                        docker push jeetu844/shopping:latest
                    '''
                    }
                }
            }
        }
    }
}
