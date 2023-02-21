def imageName = 'mlabouardy/movies-loader'

node(''){ 
    
    stage('Checkout'){
        checkout scm
    }

    stage('Unit Tests'){
        sh "docker build -t ${imageName}-test -f Dockerfile.test ."
        sh "docker run --rm ${imageName}-test"
    }


}