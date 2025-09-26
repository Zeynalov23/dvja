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

    /*
    stage('Build & Unit Tests') {
      steps { sh "mvn ${env.MVN_FLAGS} clean verify" }
      post { always { junit 'target/surefire-reports/*.xml'; publishHTML(target: [reportDir: 'target/site/jacoco', reportFiles: 'index.html', reportName: 'JaCoCo']) } }
    }
    
    stage('Static / Dependency Scans (parallel)') {
      parallel {
        stage('SpotBugs (scripted)') {
          steps { script { sh "mvn -DskipTests spotbugs:spotbugs spotbugs:check || true"; archiveArtifacts 'target/spotbugsXml.xml' } }
        }
        stage('OWASP Dep Check (stand-in for Nexus IQ)') {
          steps { sh "mvn -Dformat=XML org.owasp:dependency-check-maven:check || true"; archiveArtifacts 'target/dependency-check-report.xml' }
        }
        // If you have Teamscale/IQ: add CLI stages here to upload reports or evaluate policies.
      }
    }
    */

    stage('Package & Publish to Nexus (SNAPSHOT)') {
      when {
        not {
          branch 'release/*'
        }
      }
      steps { sh "mvn ${env.MVN_FLAGS} -DskipTests deploy -s .jenkins/settings.xml" }
    }
  }
}
