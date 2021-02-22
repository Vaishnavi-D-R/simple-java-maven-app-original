  pipeline {
    agent any
    tools{
        maven 'MAVEN-3.6.3'
        jdk 'JDK11'
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
        stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('sonar-server') {
                sh 'mvn clean package sonar:sonar'
              }
            }
          }
      stage('collect artifact'){
           steps{
               archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
           }
        }
        stage('deploy to artifactory')
           {
              steps{
                    rtUpload (
                        serverId: 'artifactory-server',
                            spec: '''{
                                "files": [
                                {
                                    "pattern": "target/*.jar",
                                    "target": "art-doc-dev-loc"
                                }
                                ]
                            }''',
                buildName: 'holyFrog',
                buildNumber: '42'
                )
            }
           }
    }
}
