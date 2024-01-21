pipeline {
    agent any
    tools{
        jdk 'jdk17'
        nodejs 'nodejs16'
    }
    environment{
        SCANNER_HOME=tool 'sonar-scanner'
        Version="${env.BUILD_ID}"
        // GithubUser="jeetu844"
        // GithubRepo="Shopping-reactJS-manifest"
        // MyName="Jitendra Sharma"
        // MyEmail="jeetu.844@gmail.com"

        GithubUser="${params.GithubUser}"
        GithubRepo=params.GithubRepo
        DockerUser=params.DockerUser
        DockerRepo=params.DockerRepo
        MyName=params.MyName
        MyEmail=params.MyEmail
    }
    stages {
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
                        docker tag shopping:$Version $DockerRepo/shopping:$Version
                        docker tag shopping:$Version $DockerRepo/shopping:latest
                        docker push $DockerRepo/shopping:$Version
                        docker push $DockerRepo/shopping:latest
                    '''
                    }
                }
            }
        }
        // stage('Trivy Image Scan'){
        //     steps{
        //         sh 'trivy image shopping:$Version > trivyimage.txt'
        //     }
        // }
        stage('Manifest Pull'){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/jeetu844/Shopping-reactJS-manifest.git'
            }
        }
        stage('Update Manifest'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'github-token', variable: 'gitcred')]) {
                        sh '''
                            sed -i "s|image: .*|image: ${DockerRepo}/shopping:${Version}|g" deployment.yml
                            git config --global user.email ${MyEmail}
                            git config --global user.name ${MyName}
                            git add deployment.yml
                            git commit -a -m "Update Manifest with $DockerRepo/shopping:$Version"
                            git push https://$gitcred@github.com/$GithubUser/$GithubRepo HEAD:main
                        '''
                    }
                }
            }
        }
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
    }
}
