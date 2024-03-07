pipeline{
    agent any
  tools{
    nodejs 'nodejs18'
    jdk 'jdk17'
  }
  environment{
      SONAR_HOME=tool 'sonarqube'
  }
  stages{
    stage("Clean Workspace"){
        steps{
            cleanWs()
        }
    }
    stage("Git checkout"){
        steps{
            git branch: 'main', url: 'https://github.com/Yatingambhir85/zomato-clone'
        }
    }
    stage("Sonarqube analysis"){
        steps{
            withSonarQubeEnv('sonarqube'){
                sh ' $SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=zomato-clone -Dsonar.projectKey=zomato-clone '
            }
        }
    }
    stage("Install Dependencies"){
        steps{
            sh ' npm install '
        }
    }
    stage("TRIVY FS SCAN"){
        steps{
            sh "trivy fs . > trivyfs.txt"
        }
    }
    stage('OWASP FS SCAN') {
        steps {
            dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
            dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        }
    }
    stage("Docker build & push"){
        steps{
            script{
               withDockerRegister(credentialsId: 'docker', toolName: 'docker'){
                   sh "docker build -t zomato-clone ."
                   sh "docker tag zomato-clone:latest"
                   sh "docker push yatingambhir/zomato-clone:latest"
               }
            }
        }
    }
    stage("TRIVY"){
      steps{
          sh "trivy image yatingambhir/zomato-clone:latest > trivy.txt"
      }
    }
    stage("Deployment"){
        steps{
            sh "docker run -itd --name zomato-clone-project -p 3000:3000 zomato-clone:latest"
        }
    }             
  }
}
            
