// DO NOT EDIT
pipeline {
    agent { docker { image 'golang' } }
    stages {
        stage('Pre Test') {
            steps{
                echo 'Pulling Dependencies'

                sh 'go version'
                sh 'go get -u github.com/golang/dep/cmd/dep'
                sh 'go get -u github.com/golang/lint/golint'
                sh 'go get github.com/tebeka/go2xunit'  
            }  
        }
        stage('Test'){
            steps{
                //List all our project files with 'go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org'
                //Push our project files relative to ./src
                sh 'cd $GOPATH && go list ./... | grep -v /vendor/ | grep -v github.com | grep -v golang.org > projectPaths'

                //Print them with 'awk '$0="./src/"$0' projectPaths' in order to get full relative path to $GOPATH
                script{
                    def paths = sh returnStdout: true, script: """awk '\$0="./src/"\$0' projectPaths"""
                }
                

                echo 'Vetting'

                sh """cd $GOPATH && go tool vet ${paths}"""

                echo 'Linting'
                sh """cd $GOPATH && golint ${paths}"""

                echo 'Testing'
                sh """cd $GOPATH && go test -race -cover ${paths}""" 
            }  
        }
    }
}