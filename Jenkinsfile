
    node {
        stage('CI') {
            
                snDevOpsStep 'cc90c672dbcfbb00b66d55535e9619d1'
                sh '''
                    export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                    export PATH=$PATH:$M2_HOME/bin
                    mvn --version
                '''
                sh 'mvn compile'
                sh 'mvn verify'
            
                    junit '**/target/surefire-reports/*.xml' 
           
        }
        stage('UAT deploy') {
           
                snDevOpsStep 'c490c672dbcfbb00b66d55535e9619d1'
                sh '''
                    export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                    export PATH=$PATH:$M2_HOME/bin
                    mvn --version
                '''
                sh 'mvn package'

                script {
                    sshPublisher(continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName:'CorpSite UAT',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/globex-web.war',
                                    removePrefix: 'target/',
                                    remoteDirectory: '/opt/tomcat/webapps'
                                )
                            ]
                        )
                    ])
                }
            
        }
        stage('UAT test') {
            parallel {
                stage('UAT unit test') {
                    
                        //snDevOpsStep '23b0c672dbcfbb00b66d55535e9619f4'
                        sh '''
                            export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                            export PATH=$PATH:$M2_HOME/bin
                            mvn --version
                        '''
                        sh 'mvn compile'
                        sh 'mvn verify'
                    
                            junit '**/target/surefire-reports/*.xml' 
                    
                }
                stage('UAT static code test') {
                    
                        sh '''
                            export M2_HOME=/opt/apache-maven-3.6.0 # your Mavan home path
                            export PATH=$PATH:$M2_HOME/bin
                            mvn --version
                        '''
                        sh 'mvn compile'
                        sh '''
                            mvn sonar:sonar \
                            -Dsonar.projectKey=CorpSite \
                            -Dsonar.host.url=http://sonarqube.sndevops.xyz:9000 \
                            -Dsonar.login=efef5144be738a606c23fff3f139f00965b82869 \
                            -Dsonar.exclusions=src/main/webapp/resources/js/bootstrap.js \
                            -Dsonar.analysis.scm=$GIT_COMMIT \
                            -Dsonar.analysis.buildURL=$BUILD_URL
                        '''
                    
                }
            }
        }
        stage('PROD') {
           
                snDevOpsStep '4090c672dbcfbb00b66d55535e9619d2'
                snDevOpsChange()
                script {                
                    sshPublisher(continueOnError: false, failOnError: true,
                    publishers: [
                        sshPublisherDesc(
                            configName:'CorpSite PROD',
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'target/globex-web.war',
                                    removePrefix: 'target/',
                                    remoteDirectory: '/opt/tomcat/webapps'
                                )
                            ]
                        )
                    ])
                }
            
        }
    }
