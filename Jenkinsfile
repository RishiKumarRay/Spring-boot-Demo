@Library('my-maven-library')_
pipeline{
   agent {
        label 'slave'
    }
     
    tools {
         jdk 'jdk'
         maven 'Maven' 
    }  
    
    environment {
        dockerhub=credentials('dockerhub')
        
    } 
    stages{
        stage('clean')
        {
            steps{
                mavenBuild()
            }
        }

        stage('test')
        {
            steps{
                runTests()
                
            }
        }

        stage('pack')
        {
            when{
                branch "Production"
                }
            steps{
                mavenPackage()
            }
        }

       
        stage('build image')
        {
            when{
                branch "Production"
                }
            steps{
                sh 'docker build -t Springboot:${GIT_COMMIT} .'
            }
        } 

        stage('pushing to dockerhub')
        {
            when{
                branch "Production"
                }
            steps{
                sh 'docker tag springboot:${GIT_COMMIT} rishiray/springboot-app:${GIT_COMMIT}'
                sh 'echo $dockerhub_PSW | docker login -u $dockerhub_USR --password-stdin'
                sh 'docker push rishiray/springboot-app:${GIT_COMMIT}'
            }
        } 
        stage('deploy')
        {
             when{
                branch "Production"
                }
            steps{
                script{
                    kubernetesDeploy configs: '**/deploy.yml', kubeConfig: [path: ''], kubeconfigId: 'kubeconfig', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
                }
            }
        }

    }
}
