pipeline{
    agent {
  label 'buildserver'
        }
    tools {
        git 'MyGit'
        maven 'myMaven'
    }
    stages{
        stage("Checkout code"){
            steps{
                echo "========executing Checkout code========"
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: 'myGitCredentials', url: 'https://github.com/gthborg/simple-java-maven-app.git']]])
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Checkout code executed successfully========"
                }
                failure{
                    echo "========Checkout code execution failed========"
                }
            }
        }
        stage("Build"){
            steps{
                echo "========executing Build========"
                sh 'mvn clean install'
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Build executed successfully========"
                    archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
                }
                failure{
                    echo "========Build execution failed========"
                }
            }
        }
        stage("Unit Test"){
            steps{
                echo "========executing Unit Test========"
                sh 'mvn test'
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Build executed successfully========"
                    junit 'target/surefire-reports/*.xml'
                }
                failure{
                    echo "========Build execution failed========"
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage("Sonar Scan"){
            steps{
                echo "========executing Sonar Scan========"
                withSonarQubeEnv('MySonarQube') {
                    sh """${tool('MySonarScanner')}/bin/sonar-scanner \
                        -Dsonar.projectKey=simple-java-maven-app \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target/* \
                        -Dsonar.host.url=http://172.31.15.240:9000 \
                        -Dsonar.login=sqp_63b1aa0db3f8ee6396151183482c74aef0e5810b"""
               }                
            }
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Build executed successfully========"
                    junit 'target/surefire-reports/*.xml'
                }
                failure{
                    echo "========Build execution failed========"
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage("Upload Artifacts"){
            steps{
                echo "========executing Sonar Scan========"
                withMaven(globalMavenSettingsConfig: '3f28e200-322b-4c81-8b73-c151d8979f48', jdk: 'JDK8', maven: 'myMaven') {
                        sh 'mvn deploy'  
                }
              
            }               
            post{
                always{
                    echo "========always========"
                }
                success{
                    echo "========Artifacts uploaded successfully========"
                    
                }
                failure{
                    echo "========Artifacts upload failed========"
               
                }
            }
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
            echo "========pipeline executed successfully ========"
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}
