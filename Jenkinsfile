pipeline {
    agent any
    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "M3"
    }

    stages {
        stage('SonarCloud') {
            environment {
                SCANNER_HOME = tool 'sonarscanner'
                ORGANIZATION = "karlosarr"
            }
            steps {
                withSonarQubeEnv(installationName: 'SonarCloud') {
                    sh "mvn -Dmaven.test.failure.ignore=true verify org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=karlosarr_simple-maven-project-with-tests"
                }
            }
        }
        stage('Build') {
            steps {
                withSonarQubeEnv(installationName: 'SonarCloud') {
                    // Run Maven on a Unix agent.
                    sh "mvn -Dmaven.test.failure.ignore=true clean package"
                }

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
    }
}
