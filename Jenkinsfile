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
            withSonarqubeEnv('sonarqube'){
                sh ''' $SONAR_HOME/bin/sonar-scanner -Dsonar.projectName=zomato-clone -Dsonar.projectKey=zomato-clone '''
            }
        }
    }
    
            
