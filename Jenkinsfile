def imageName = 'mlabouardy/movies-loader'
def myimageName = 'monarene/movie-loader'
// Try finsing tests again


node(''){ 
    
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

}


def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
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