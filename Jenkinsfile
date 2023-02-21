def imageName = 'mlabouardy/movies-loader'

node(''){ 
    
    stage('Checkout'){
        checkout scm
    }

    stage('Unit Tests'){
        sh "sudo docker build -t ${imageName}-test -f Dockerfile.test ."
        sh "sudo docker run --rm ${imageName}-test"
    }


}