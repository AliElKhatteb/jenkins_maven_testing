node {
   def mvnHome
   stage('Preparation') { // for display purposes
      // Get some code from a GitHub repository
      git 'https://github.com/AliElKhatteb/jenkins_maven_testing.git'
      // Get the Maven tool.
      // ** NOTE: This 'M3' Maven tool must be configured
      // **       in the global configuration.           
      mvnHome = tool 'M3'

   }
   stage('Build') {
      // Run the maven build
      withEnv(["MVN_HOME=$mvnHome"]) {
         if (isUnix()) {
            sh '"$MVN_HOME/bin/mvn" -Dmaven.test.failure.ignore clean package'
         } else {
            bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore clean package/)

         }
      }
   }
    stage ('sonarqube analysis'){
    withEnv(["MVN_HOME=$mvnHome"]) {
       bat(/"%MVN_HOME%\bin\mvn" -Dmaven.test.failure.ignore verify sonar:sonar/)
       }
      }
      stage ('OWASP Dependency-Check Vulnerabilities') {
                  steps {
                      dependencyCheck additionalArguments: '''
                          -o "./"
                          -s "./"
                          -f "ALL"
                          --prettyPrint''', odcInstallation: 'OWASP-DC'

                      dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                  }
   stage('Results') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archiveArtifacts 'target/*.jar'
   }

}
