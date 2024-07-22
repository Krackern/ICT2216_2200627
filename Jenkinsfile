pipeline {
     agent any

    stages {
        stage('Checkout SCM') {
            steps {
                git(url: 'https://github.com/Krackern/ICT2216_2200627.git', branch: 'main')
            }
        }
         
        stage('Static Code Analysis with SonarQube') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube';
                    withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=ICT2216LabTest -Dsonar.sources=src"
                    }
                }
            }
        }

        stage('OWASP Dependency-Check Vulnerabilities') {
  	        steps {
    	      dependencyCheck additionalArguments: '''
                	-o './'
                	-s './'
                	-f 'ALL'
                	--prettyPrint''', odcInstallation: 'OWASP-Dependency-Check'
   	 
    	dependencyCheckPublisher pattern: 'dependency-check-report.xml'
  	}
	}

    
     
         
    }
    
    post {
        success {
            echo "Pipline Success!"
        }
        failure {
            echo "Pipline Failed!"
        }
    }
    
}
