pipeline {

    agent any

    tools { 
        maven 'my-maven' 
    }

    parameters {
        string(name: 'PERSON', defaultValue: 'Mr Jenkins', description: 'Who should I say hello to?')

        text(name: 'BIOGRAPHY', defaultValue: '', description: 'Enter some information about the person')

        booleanParam(name: 'TOGGLE', defaultValue: true, description: 'Toggle this value')

        choice(name: 'CHOICE', choices: ['One', 'Two', 'Three'], description: 'Pick something')

        password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Enter a password')

        string(name: 'MYNAME', defaultValue: 'BI', description: 'my name is BI')
    }

    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
        NAME = 'DINHLE'
        HOVATEN = 'DINHLEHOANG'
        abc = 'asdf'
    }
    stages {

        stage('Initialize') {

            environment {
                NAME = 'HOANG'
            }
            steps {
                // sh 'sudo apt install maven'
                withSonarQubeEnv(installationName: 'my-sonar') {
                    sh 'mvn clean install -Dmaven.test.skip=true sonar:sonar'
                }

            }
        }

        stage('Build in maven') {
            // agent {
            //     docker {
            //         image 'maven:latest'
            //     }
            // }
            steps {
                sh 'echo $HOVATEN'
                echo "Hello ${params.MYNAME}"
                echo 'Building nginx image..'
                sh 'mvn --version'
                sh 'pwd'
                sh 'ls -la'
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                stash includes : 'target/*.jar', name: 'app'
                
            }
        }

        stage('Package to docker image') {

            steps {
                unstash 'app' 
                sh 'ls -la'
                sh 'ls -la target'
                sh 'docker build -t hoangledinh65/springboot-image:1.0 .'
            }
        }
        stage('Pushing image') {
            steps {
                sh 'echo $HOVATEN'
                echo 'Start pushing.. with credential'
                sh 'echo $DOCKERHUB_CREDENTIALS'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push hoangledinh65/springboot-image:1.0'
                
            }
        }
        stage('Deploying and Cleaning') {
            steps {
                echo 'Deploying and cleaning'
                sh 'docker image rm hoangledinh65/springboot-image:1.0 || echo "this image does not exist" '
                sh 'docker container stop my-demo-springboot || echo "this container does not exist" '
                sh 'docker network create jenkins || echo "this network exists"'
                sh 'echo y | docker container prune '
                sh 'echo y | docker image prune'
                sh 'docker container run -d --rm --name my-demo-springboot -p 8082:8080 --network jenkins hoangledinh65/springboot-image:1.0'
                sh 'echo hoangledinh65'
            }
        }
        // stage('Ansible') {
        //     steps {
        //         sh 'cat hosts'
        //         sh 'cat ec2-instance-playbook.yaml'
        //         sh 'chmod 400 aws-key.pem'
        //         sh 'ansible-playbook ec2-instance-playbook.yaml -i hosts --private-key aws-key.pem'
        //         // ansiblePlaybook become: true, credentialsId: 'agent-credential', installation: 'my-ansible', inventory: 'hosts', playbook: 'ec2-instance-playbook.yaml'
        //     }
        // }

    }
}
