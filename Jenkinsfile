pipeline {
  // Possible agent configurations - you must have one and only one at the top level.
  agent any
  
  // Global Environment
  environment {
    DEPLOY2TEST = false
    DEPLOY2PROD = false
    DEPLOY_VERSION = "0.0.0"
  }
  
  options {
    timestamps()
    timeout(time:5, unit:'MINUTES')
    buildDiscarder(logRotator(numToKeepStr:'5'))
    disableConcurrentBuilds()
    //skipDefaultCheckout()
  }
  
  // Tools - only works when *not* on docker or dockerfile agent
  tools {
    // Symbol for tool type and then name of configured tool installation
    maven 'Maven3' 
    jdk 'JDK8'
  }
  
  //triggers { cron('@daily') }
  
  stages {

    
    stage ('Build') {
      steps {
        sh 'mvn clean test -U'
      }
    }
    
    stage('Integration test') {
      steps {
         sh 'mvn verify'
      }
    }
    stage('Site') {
      steps {
         sh 'mvn site'
      }
    }
    
    stage('Archive') {
      steps {
        sh 'mvn deploy'
        
        junit '**/target/surefire-reports/TEST-*.xml'
        archive 'target/*.war'
      }
    }

    stage('Dev Deploy') {
      when { branch "dev"}
      steps {
         echo "Deploy to dev environment"
      }
    }
    
    stage('Release') {
      when { branch "release"}
      steps { 
        echo "Release"
      }
    }
    
    
    //Sonarqube analysis on master and dev branch
    stage('SonarMaster') {
      when { branch "master" }
      steps { 
        expression {
          withSonarQubeEnv('sonarqube') {
            sh 'mvn  sonar:sonar -DargLine="-Xmx256m"'
          }
        }
      }
    }
    
    stage('SonarDev') {
      when { branch "master" }
      steps { 
        expression {
          withSonarQubeEnv('sonarqube') {
            sh 'mvn  sonar:sonar -DargLine="-Xmx256m" -Dsonar.branch="dev"'
          }
        }
      }
    }
    
  }
}
