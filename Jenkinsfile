pipeline { 
    agent none
    tools {  
        jdk   'JDK11'
        maven 'usemaven'

    }

parameters {
        string(name: 'ENV', defaultValue: 'Test', description: 'Version to deploy')

        booleanParam(name: 'executetests', defaultValue: true, description: 'decide to run test case')

        choice(name: 'Appversion', choices: ['1.1', '1.2', '1.3'])
    }
    stages {
        stage('compile') {
            agent any
            steps {
                echo 'compiling the java based code'
                echo "compiling the ${params.ENV}"
                sh 'mvn compile'
            } 
        }
        stage('Unittest') {
           agent any
            when {
                expression{
                    params.executetests == true
                }
            }
            steps {
                echo 'testing the code'
                sh 'mvn test'
            }
            post{
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            
            }
        }
        stage('package') {
            agent any
            input {
                message "select the version?"
                ok "version selected"
                parameters {
                    choice(name: 'Appversion', choices: ['1.1', '1.2', '1.3'])
                }
            }
            steps{
            script{
                sshagent(['deploy-server']) {
                echo"packaging the code"
                sh "scp -o StrictHostKeyChecking=no server-config.sh ec2-user@172.31.18.54:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.18.54 'bash ~/server-config.sh'"
            }
            }
            }
            }
        }
    }

