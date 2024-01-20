pipeline {
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
        Version="${env.BUILD_ID}"
        GithubUser="jeetu844"
        GithubRepo="Shopping-reactJS-DevOps"
        MyName="Jitendra Sharma"
        MyEmail="jeetu.844@gmail.com"
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
        // stage('Docker Image'){
        //     steps{
        //         script{
        //             withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {
        //             sh '''
        //                 docker build -t shopping:$Version .
        //                 docker tag shopping:$Version jeetu844/shopping:$Version
        //                 docker tag shopping:$Version jeetu844/shopping:latest
        //                 docker push jeetu844/shopping:$Version
        //                 docker push jeetu844/shopping:latest
        //             '''
        //             }
        //         }
        //     }
        // }
        // stage('Trivy Image Scan'){
        //     steps{
        //         sh 'trivy image shopping:$Version > trivyimage.txt'
        //     }
        // }
        stage('Update Manifest'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'github-token', variable: 'gitcred')]) {
                        sh '''
                            sed -i 's|image: .*|image: jeetu844/shopping:${Version}|g' deployment.yml
                            git config --global user.email '"${MyEmail}"'
                            git config --global user.name '"${MyName}"'
                            git add .
                            git commit -a -m "Update Manifest with jeetu844/shopping:${Version}"
                            git push https://$gitcred@github.com/$GithubUser/$GithubRepo HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
