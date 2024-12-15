node {
    stage('Git SCM') {
        // Check out the source code from the Git repository
        checkout scm
    }

    stage('Installing project dependencies and running a dependency scan using Snyk') {
        // Use Jenkins credentials to fetch Snyk API token securely
        withCredentials([string(credentialsId: 'SYNK', variable: 'SNYK_TOKEN')]) {
            // Install project dependencies
            sh "npm install"

            // Authenticate and monitor project with Snyk
            sh '''
                export SNYK_TOKEN=$SNYK_TOKEN
                snyk auth $SNYK_TOKEN > /dev/null 2>&1
                snyk monitor --org=36b48543-9691-48ee-b733-73377ad34d2c
            '''
        }
    }

    stage('Source Code Analysis using SonarQube') {
        // Use Jenkins credentials to fetch SonarQube token securely
        withCredentials([string(credentialsId: 'sonarqube', variable: 'envsonar')]) {
            // Define the SonarQube scanner tool
            def scannerHome = tool(name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation')
            
            // Set up SonarQube environment and run scanner
            withSonarQubeEnv('envsonar') {
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }
}
