pipeline {
    agent any
    
    tools {
          maven 'maven'
        }

    stages {
        stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar') {
                sh 'mvn clean package sonar:sonar -Dsonar.host.url=http://13.88.184.36:9000/ -Dsonar.login=admin -Dsonar.password=devops123 -Dsonar.sources=. -Dsonar.tests=. -Dsonar.inclusions=**/test/java/servlet/createpage_junit.java -Dsonar.test.exclusions=**/test/java/servlet/createpage_junit.java'
              }
            }
          }
          stage("Quality Gate") {
            steps {
              timeout(time: 1, unit: 'HOURS') {
                waitForQualityGate abortPipeline: true
              }
            }
          }
        stage('Artifactory configuration'){
            steps {
                script{
                    // Get Artifactory server instance, defined in the Artifactory Plugin administration page.
                    def server = Artifactory.server "artifactory"
                    // Create an Artifactory Maven instance.
                    def rtMaven = Artifactory.newMavenBuild()
                    //def buildInfo
                    // Tool name from Jenkins configuration
                    rtMaven.tool = "Maven-3.3.9"
                    // Set Artifactory repositories for dependencies resolution and artifacts deployment.
                    rtMaven.deployer releaseRepo:'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
                    rtMaven.resolver releaseRepo:'libs-release', snapshotRepo:'libs-snapshot', server: server
                }
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
        stage('Perfomance Testing') {
            steps {
                blazeMeterTest credentialsId: 'blazemeterdemo', testId: '7907341.taurus', workspaceId: '475906'
            }
        }
    }
}
