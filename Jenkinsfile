node {
    stage('Git SCM') { # Check out the source code from the repository defined in Jenkins.
        checkout scm
    }
    
    stage('Installing project dependencies and running a dependency scan using Snyk') {
        withCredentials([string(credentialsId: 'token', variable: 'SNYK_TOKEN')]) { # Retrieve the Snyk token using the credential ID "token" stored in Jenkins.
            sh "npm install" # Install the project dependencies.

            sh '''
                export SNYK_TOKEN=$SNYK_TOKEN
                snyk auth $SNYK_TOKEN > /dev/null 2>&1 # Authenticate Snyk using the token. Output is redirected to /dev/null to hide sensitive information in logs.
                snyk monitor --org=a16b592a-4677-4b3c-ab8a-b034d23d37db # Monitor project dependencies for vulnerabilities. The --org flag specifies the Snyk organization, required if multiple organizations exist.
            '''
        }
    }

    stage('Source Code Analysis using SonarQube') {
       withCredentials([string(credentialsId: 'sonarqube', variable: 'envsonar')]) { # Retrieve SonarQube credentials using the credential ID "sonarqube" stored in Jenkins.
            def scannerHome = tool(name: 'SonarQube', type: 'hudson.plugins.sonar.SonarRunnerInstallation') # Define the path to the SonarQube scanner, configured using the tool name in Jenkins.
            withSonarQubeEnv('envsonar') { # Set up the environment for SonarQube analysis using the retrieved credentials.
                sh "${scannerHome}/bin/sonar-scanner" # Execute the SonarQube analysis.
            }
        }
    }
}
