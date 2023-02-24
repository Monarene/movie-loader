def imageName = 'mlabouardy/movies-loader'
def myimageName = 'monarene/movie-loader'

environment {
		DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred-raja')
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
        docker.build(myimageName)
    }

    stage('Push'){
        sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
    }


}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}