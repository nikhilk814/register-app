pipeline {
    agent { label 'kslave1-agent' }
    tools {
        jdk 'Java17'
        maven 'maven'
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'master', credentialsId: 'github', url: 'https://github.com/nikhilk814/register-app.git'
                }
        }

        stage("Build Application"){
            steps {
                sh "clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "test"
           }
       }
    }
}
