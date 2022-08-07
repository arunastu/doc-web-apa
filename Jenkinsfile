pipeline 
{
    agent any
    environment 
	{
      DOCKER_TAG = getVersion()
    }
    
    stages
    {
        stage ('Get the Code from the Code Base')
        {
            steps {
                echo "Get the code from the GitHub"
                sh 'git rev-parse --short HEAD'
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/arunastu/doc-web-apa'
            }
        }
                stage('Docker Build')
        {
            steps{
                sh "whoami"
                sh "docker build . -t arunastu/mywebapp:${DOCKER_TAG}"
            }
        }
        
        stage('Push the image to Docker Hub')
        {
            steps
            {
                withCredentials([string(credentialsId: 'dockerHubPwd', variable: 'dockerHubPwd')])
                {
                    sh "docker login -u arunastu -p ${dockerHubPwd}"
                }
                sh "docker push arunastu/mywebapp:${DOCKER_TAG} "
            }
        }

        stage ('Docker image deployment using the Ansible')
        {
            steps
            {
                ansiblePlaybook become: true, credentialsId: 'sshpwd', disableHostKeyChecking: true, installation: 'Ansible',  inventory: 'cicd.inv', playbook: 'cicd.yml'
            }
        }
    }
}
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}    
