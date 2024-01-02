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
    parameters { 
        choice(name: 'HELM_CHART', choices: choiceArray, description: 'Please Select Helm Chart to build') 
        choice(name: 'BUILD_TYPE', choices: ['main', 'development'], description: 'Please Select Build Type')
        booleanParam(name: 'RELEASE', defaultValue: false, description: 'Select to release to the Repo')
    }
    
    stages {
        stage('Prepare') {
            steps {
                echo "Selected helm chart is: ${params.HELM_CHART}"
                dir ("charts/${params.HELM_CHART}") {
                    sh 'ls -lah'
                }
            }
        }
        
        stage('Test') {
            steps {
                echo "Testing: ${params.HELM_CHART}"
                dir ("charts") {
                    sh "helm lint ${params.HELM_CHART}"
                }
            }
        }

        stage('Build') {
            steps {
                echo "Building: ${params.HELM_CHART}"
                dir ("charts") {
                    sh "helm package ${params.HELM_CHART} -d ../docs/${params.BUILD_TYPE}"
                    sh "helm repo index ../docs/${params.BUILD_TYPE}"
                }
            }
        }
        

        stage('Release') {
            when {
                expression {
                    params.RELEASE == true
                }
            }
            steps {
                echo "Releasingg: ${params.HELM_CHART}"
                dir ("docs/${params.BUILD_TYPE}") {
                    sh 'ls -lah'
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