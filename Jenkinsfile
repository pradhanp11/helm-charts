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
    environment {
        def BUILD_VERSION = sh(script: "echo `date +%y.%m.%d`${env.BUILD_NUMBER}", returnStdout: true).trim()
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
            when {
                expression {
                    params.RELEASE == false
                }
            }
            steps {
                echo "Building: ${params.HELM_CHART}  Version: $BUILD_VERSION"
                dir ("charts") {
                    sh "helm package ${params.HELM_CHART} --version $BUILD_VERSION"
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
                echo "Releasing: ${params.HELM_CHART} "

                dir ("charts") {
                    sh "rm *.tgz || true"
                    sh "helm package ${params.HELM_CHART} -d ../docs/${params.BUILD_TYPE} --version $BUILD_VERSION"
                    sh "helm repo index ../docs/${params.BUILD_TYPE}"
                }
                
                dir ("docs/${params.BUILD_TYPE}") {
                    script {
                        catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                            withCredentials([usernamePassword(credentialsId: 'Github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                                echo "GIT Username: $GIT_USERNAME"
                                // def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                                def HELM_FILE = sh(script: "echo ${params.HELM_CHART}-${BUILD_VERSION}.tgz", returnStdout: true).trim()
                                sh "git config user.email pradhanp@gmail.com"
                                sh "git config user.name pradhanp"
                                sh "git add index.yaml $HELM_FILE"
                                sh "git commit -m 'Triggered Build: $BUILD_VERSION'"
                                //sh "git push https://${GIT_USERNAME}:${encodedPassword}@github.com/${GIT_USERNAME}/helm-charts.git"
                                sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/helm-charts.git HEAD:main"
                            }
                        }
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