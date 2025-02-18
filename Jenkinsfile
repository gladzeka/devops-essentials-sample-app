pipeline {
    agent any
    stages {
         
        stage('Build') {
            steps {
                echo 'Running build automation'
                bat './gradlew build'
                archiveArtifacts artifacts: 'src/index.html'
            }
        }
      
        stage('DeployToStage') {
            when {
                branch 'master'
            }
            steps {
                withCredentials([string(credentialsId: '', variable: '')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: 'cloud_user',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src/**',
                                        removePrefix: 'src/'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
        stage('DeployToProd') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment is looking fine?'
                milestone(1)
                withCredentials([string(credentialsId: '', variable: '')]) {
                    sshPublisher(
                        failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'production',
                                sshCredentials: [
                                    username: 'cloud_user',
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'src/**',
                                        removePrefix: 'src/'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
        }
    }
}