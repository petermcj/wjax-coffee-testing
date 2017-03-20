pipeline {
  // Possible agent configurations - you must have one and only one at the top level.
  agent any
  
  parameters {
    booleanParam(name: 'RUN_SONAR', defaultValue: false, description: 'RUN SONAR ANALYSIS?')
  }
  
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
    
    stage('Sonar') {
      when { 
        expression {
          return  params.RUN_SONAR
        }
      }
      
      steps { 
        withSonarQubeEnv('sonarqube') {
          sh 'mvn  sonar:sonar -DargLine="-Xmx256m" -Dsonar.branch="${BRANCH_NAME}"'
        }
      }
    }
    
  }
  
  post {
    always {
      archive "target/**/*.[ear|war]"
      junit 'target/surefire-reports/*.xml'
    }
   }
}
