def imageName = 'mlabouardy/movies-loader'
def myimageName = 'monarene/movie-loader'

environment {
		DOCKERHUB_CREDENTIALS = credentials('dockerhub')
	}

node(''){ 
    
    stage('Checkout'){
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        sh "docker run --rm -v $PWD/reports:/app/reports ${imageName}-test"
        junit "reports/*.xml"
    
    }

    stage('Build'){
        dockerImage = docker.build(myimageName)
    }

    stage('Push'){
        withDockerRegistry([credentialsId: "dockerhub", url: "" ]) {
        dockerImage.push(commitID())}
    }


}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}