#!groovy?
def GIT_COMMIT = ""
def GIT_COMMIT_SHORT = ""
node {
deleteDir()
stage('Descargar Fuentes') {
	checkout scm
	script {
	    GIT_COMMIT = sh (
		script: 'git rev-parse HEAD',
		returnStdout: true
	    ).trim()	
	    GIT_COMMIT_SHORT = GIT_COMMIT.substring(0,8)
	}	
}
stage('Build And Push') {
	sh "whoami"
	sh "ls -l"
	sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 379024051677.dkr.ecr.us-east-1.amazonaws.com"
	sh "docker build -t reto-app ."
	sh "docker tag reto-app:${GIT_COMMIT_SHORT} 379024051677.dkr.ecr.us-east-1.amazonaws.com/reto-app:${GIT_COMMIT_SHORT}"
	sh "docker push 379024051677.dkr.ecr.us-east-1.amazonaws.com/reto-app:${GIT_COMMIT_SHORT}"
	
}
stage('Deploy Kubernetes') {	
	sh "echo Iniciando"
        sh "sed -i 's/#{TAG}/${GIT_COMMIT_SHORT}/g' $WORKSPACE/deployment.yaml"
	sh "aws eks update-kubeconfig --name eks-terraform --region us-east-1"		
	sh "kubectl apply -f deployment.yaml"		
	sh "kubectl get svc"		

}

}
