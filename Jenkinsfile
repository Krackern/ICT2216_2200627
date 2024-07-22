pipeline {
     agent any

    stages {
        // stage('Checkout SCM') {
        //     steps {
        //         git(url: 'https://github.com/Krackern/ICT2216_2200627.git', branch: 'main')
        //     }
        // }

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

        stage('Static Code Analysis with Maven') {
            steps {
                script {
                    sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
                    sh '/var/jenkins_home/apache-maven-3.9.8/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
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
        always {
          junit testResults: '**/target/surefire-reports/TEST-*.xml'
          recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
          recordIssues enabledForFailure: true, tool: checkStyle()
          recordIssues enabledForFailure: true, tool: spotBugs(pattern:'**/target/findbugsXml.xml')
          recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
          recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
        }
        success {
            echo "Pipline Success!"
        }
        failure {
            echo "Pipline Failed!"
        }
    }
}