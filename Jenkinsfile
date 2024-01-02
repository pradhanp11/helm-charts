def choiceArray = []
node {
    checkout scm
    def folders = sh(returnStdout: true, script: "ls $WORKSPACE/charts")

    folders.split().each {
        // condition to skip files if any
        choiceArray << it
    }
}

pipeline {
    agent any
    parameters { choice(name: 'CHOICES', choices: choiceArray, description: 'Please Select One') }
    stages {
        stage('Prepare') {
            steps {
                echo "Selected helm chart is: ${params.CHOICES}"
                sh "cd $WORKSPACE/charts/${params.CHOICES}"
                sh 'ls -lah'
            }
        }
        
        stage('Build') {
            steps {
                echo "Building: ${params.CHOICES}"
                sh '''

                    ls -lah
                '''
            }
        }
        
        stage('Test') {
            steps {
                echo "Testing: ${params.CHOICES}"
                sh "helm lint ."
            }
        }

        stage('Release') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    retry(5) {
                        sh 'echo "Deploying"'
                    }
                }
            }
        }
    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
    }
}