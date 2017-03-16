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

    stage ('Info') {
      steps {
        echo "env.BRANCH_NAME: $env.BRANCH_NAME"
        echo branch
      }
    }
   
  }
}
