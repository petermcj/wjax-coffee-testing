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
    timeout time:5, unit:'MINUTES'
    buildDiscarder(logRotator(numToKeepStr:'5'))
    disableConcurrentBuilds()
    //skipDefaultCheckout()
  }
  
  // Tools - only works when *not* on docker or dockerfile agent
  tools {
    // Symbol for tool type and then name of configured tool installation
    maven 'Maven3' //Default maven settings i set on Jenkins!
    jdk 'JDK8'
  }
  
  stages {
    stage ('Build') {
      steps {
        sh 'mvn clean test -U'
      }
    }
    
    stage('Integration test') {
      steps {
         sh 'mvn failsafe:integration-test'
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
