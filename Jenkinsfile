pipeline {
  agent any
  options { timestamps() }
  parameters {
    choice(name: 'ENV', choices: ['dev','test'], description: 'Target env')
    booleanParam(name: 'RUN_GATLING', defaultValue: false, description: 'Run perf smoke')
    string(name: 'RELEASE_VERSION', defaultValue: '1.0.0', description: 'For release job')
  }
  environment {
    MVN_FLAGS = '-B -U -DskipITs=false'
  }
  stages {
    stage('Checkout') { steps { checkout scm } }
    stage('Build & Unit Tests') {
      steps { sh "mvn ${env.MVN_FLAGS} clean verify" }
      post { always { junit 'target/surefire-reports/*.xml'; publishHTML(target: [reportDir: 'target/site/jacoco', reportFiles: 'index.html', reportName: 'JaCoCo']) } }
    }
  }
  
}
