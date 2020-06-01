node {

def mvnHome = tool 'Maven3'
stage ('Checkout') {

checkout scm
}
stage ('Build') {
sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
}
stage ('Code Quality Scan') {
withSonarQubeEnv('SonarQube') {
sh "${mvnHome}/bin/mvn sonar:sonar -f MyWebApp/pom.xml"
   }
}
    stage ('Nexus Upload')
    {
        nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '18.223.186.61:8081',
        groupId: 'com.mkyong',
        version: '1.0-SNAPSHOT',
        repository: 'maven-snapshots',
        credentialsId: '0c1cc176-219b-4646-847f-ec86a5a279fe',
        artifacts: [
            [artifactId: 'MyWebApp',
             classifier: '',
             file: 'MyWebApp/target/MyWebApp.war',
             type: 'war']
        ]
     )
    }

   stage ('DEV Deploy')  {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat8(credentialsId: '92ca5b91-7015-492c-ae6b-dbdd3c38f165', path: '', url: 'http://localhost:8090')], contextPath: null, war: '**/*.war'
    }
stage ('DEV Approve') {
echo "Taking approval from DEV"
timeout(time: 7, unit: 'DAYS') {
input message: 'Do you want to deploy?', submitter: 'admin'
     }
 }


stage ('Slack Notification') {


    slackSend(channel:'june2020jenkinscollab', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")


  }

}
