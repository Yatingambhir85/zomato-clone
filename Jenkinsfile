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
    stage('OWASP Dependency Check'){
        steps{
            dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
            dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
        }
    }
    stage("TRIVY FS SCAN"){
        steps{
            sh "trivy fs . > trivyfs.txt"
        }
    }
    stage("Docker build & push"){
        steps{
            withDockerRegister(credentialsId: '3756914b-1424-40a8-8842-f2dfd9452deb', toolName: 'docker'){
                sh "docker build -t zomato-clone ."
                sh "docker tag zomato-clone:latest"
                sh "docker push yatingambhir/zomato-clone:latest"
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
            
