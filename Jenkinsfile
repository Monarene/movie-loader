def imageName = 'mlabouardy/movies-loader'
def myimageName = 'monarene/movie-loader'
// Try finsing tests again

node(''){ 
    
    try {
        stage('Checkout'){
        checkout scm
    }

    stage('Unit Tests'){
        def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
        sh "docker run --rm -v $PWD/reports:/app/reports ${imageName}-test"
        junit allowEmptyResults: true, testResults: "reports/*.xml"
    
    }

    stage('Build'){
        dockerImage = docker.build(myimageName)
    }

    stage('Push'){
        withDockerRegistry([credentialsId: "dockerhub", url: "" ]) {
        dockerImage.push(commitID())
        
        if (env.BRANCH_NAME == 'develop') {
            dockerImage.push('develop')
        }
        
        }
        
    }

    stage('Deploy'){
            if(env.BRANCH_NAME == 'develop' || env.BRANCH_NAME == 'preprod'){
                build job: "watchlist-deployment/${env.BRANCH_NAME}"
            }

            if(env.BRANCH_NAME == 'master'){
                timeout(time: 2, unit: "HOURS") {
                    input message: "Approve Deploy?", ok: "Yes"
                }
                build job: "watchlist-deployment/master"
            }
        }

    
    } 
    catch(e){
        currentBuild.result = 'FAILED'
        throw e
    } finally {
        notifySlack(currentBuild.result)
    }

    

}

def notifySlack(String buildStatus){
    buildStatus =  buildStatus ?: 'SUCCESSFUL'
    def colorCode = '#FF0000'
    def subject = "Name: '${env.JOB_NAME}'\nStatus: ${buildStatus}\nBuild ID: ${env.BUILD_NUMBER}"
    def summary = "${subject}\nMessage: ${commitMessage()}\nAuthor: ${commitAuthor()}\nURL: ${env.BUILD_URL}"

    if (buildStatus == 'STARTED') {
        colorCode = '#546e7a'
    } else if (buildStatus == 'SUCCESSFUL') {
        colorCode = '#2e7d32'
    } else {
        colorCode = '#c62828c'
    }

    slackSend (color: colorCode, message: summary)
}


def commitAuthor(){
    sh 'git show -s --pretty=%an > .git/commitAuthor'
    def commitAuthor = readFile('.git/commitAuthor').trim()
    sh 'rm .git/commitAuthor'
    commitAuthor
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}

def commitMessage() {
    sh 'git log --format=%B -n 1 HEAD > .git/commitMessage'
    def commitMessage = readFile('.git/commitMessage').trim()
    sh 'rm .git/commitMessage'
    commitMessage
}

// Example for pushing to docker
// stage('Push image') {
//     withCredentials([usernamePassword( credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASSWORD')]) {
//         def registry_url = "registry.hub.docker.com/"
//         bat "docker login -u $USER -p $PASSWORD ${registry_url}"
//         docker.withRegistry("http://${registry_url}", "docker-hub-credentials") {
//             // Push your image now
//             bat "docker push username/foldername:build"
//         }
//     }
// }