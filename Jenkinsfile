  /* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any

    // tools {
    // }

    environment {
        /* groovylint-disable-next-line UnnecessaryGetter */
        DOCKER_TAG = getVersion()
    }
    stages {
        stage('checkout scm')

  {
            steps {
                git credentialsId: 'gitlab', url: 'https://gitlab.com/sreenuyys/my-app.git'
            }
  }
        stage('SonarQube analysis') {
            steps {
                    script {
                        /* groovylint-disable-next-line NoDef, UnusedVariable, VariableTypeRequired */
                        def scannerHome = tool'Sonarscanner'
                        /* groovylint-disable-next-line NestedBlockDepth */
                        withSonarQubeEnv('sonarcube1') {
                            sh 'mvn sonar:sonar'
                        }
        }   }
            }

        // stage('Quality Gate Status Check') {
        //     steps {
        //         script {
        //             /* groovylint-disable-next-line NestedBlockDepth */
        //             timeout(time: 1, unit: 'HOURS') {
        //             /* groovylint-disable-next-line NoDef, VariableTypeRequired */
        //                 def qg = waitForQualityGate()
        //             /* groovylint-disable-next-line NestedBlockDepth */
        //                 if (qg.status != 'OK') {
        //                     //    slackSend baseUrl: 'https://hooks.slack.com/services/',
        //                     //    channel: '#jenkins-pipeline-demo',
        //                     //    color: 'danger',
        //                     //    message: 'SonarQube Analysis Failed',
        //                     //    teamDomain: 'javahomecloud',
        //                     //    tokenCredentialId: 'slack-demo'
        //                     error "Pipeline aborted due to quality gate failure: ${qg.status}"
        //                 }
        //             }
        //         }
        //     }
        // }
        stage('mvn build')

  {
            steps {
                sh 'mvn clean package'
                sh 'mv target/javaaap*.war  target/javaaap.war'
            }
  }

        // stage ('deploy to tomcat') {
        //     steps {
        //         sshagent(['tomcat1']) {
        //             /* groovylint-disable-next-line LineLength, UnnecessaryGString */
        //             sh "scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/Java/target/*.war  root@192.168.56.23:/opt/tomcatA/webapps/"
        //         }
        //     }
        // }

//         stage('selenium testing')

        //   {
        //             steps {
        //                 sh 'java – cp bin ;lib/* org.testng.TestNG TestNG.xml'
        //             }
        //   }
        // stage('test') {
        //     steps {
        //         sh 'mvn -Dmaven.test.failure.ignore=true -DtestFailureIgnore=true test'
        //     }
        //     post {
        //         success {
        //             junit '**/target/surefire-reports/**/*.xml'
        //             sh "test ${currentBuild.currentResult} != UNSTABLE"
        //         }
        //     }
        // }

        // stage(' Deploy to Nexus ') {
        //                 /* groovylint-disable-next-line NestedBlockDepth */
        //     steps {
        //                     /* groovylint-disable-next-line NestedBlockDepth */
        //         script {
        //                         /* groovylint-disable-next-line LineLength */
        //             nexusArtifactUploader artifacts: [[artifactId: 'javaapp', classifier: '', file: 'target/javaaap-0.0.15-SNAPSHOT.war', type: 'war']],
        //                        credentialsId: 'nexuspass', groupId: 'com.sreenivas',
        //                         nexusUrl: '192.168.56.20:8081', nexusVersion: 'nexus3', protocol: 'http',
        //                          repository: 'maven-snapshots', version: '0.0.15-SNAPSHOT'
        //         }
        //     }
        // }

        stage('Build Docker Imager') {
            steps {
                sh "docker build . -t sreenuyys/feb:${DOCKER_TAG}"
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([string(credentialsId: 'dockpassd', variable: 'dockerpass')]) {
                    sh "docker login -u sreenuyys -p ${dockerpass}"
                }
                sh "docker push sreenuyys/feb:${DOCKER_TAG}"
            }
        }

        stage('Ansible Docker Deploy') {
            steps {
                /* groovylint-disable-next-line LineLength */
                ansiblePlaybook credentialsId: 'awskey', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}" ,installation: 'Ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
        }
    }
/* groovylint-disable-next-line MethodReturnTypeRequired, NoDef */
def getVersion() {
    /* groovylint-disable-next-line NoDef, VariableTypeRequired */
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}

