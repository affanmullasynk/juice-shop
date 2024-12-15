node {
    stage('Git SCM') {
        checkout scm
    }
    
    stage('Installing project dependencies and running a dependency scan using Snyk') {
        withCredentials([string(credentialsId: 'SYNK', variable: 'SNYK_TOKEN')]) {
            sh "npm install"
            sh '''
                export SNYK_TOKEN=$SNYK_TOKEN
                snyk auth $SNYK_TOKEN > /dev/null 2>&1
                snyk monitor --org=a16b592a-4677-4b3c-ab8a-b034d23d37db
            '''
        }
    }

    stage('Source Code Analysis using SonarQube') {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'envsonar')]) {
            def scannerHome = tool(name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation')
            withSonarQubeEnv('envsonar') {
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }
}
