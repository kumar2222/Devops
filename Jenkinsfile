pipeline { 

  environment { 

      registry = "banalam/european" 

      registryCredential = 'banalam_id' 

      dockerImage = 'latest' 

  }

  agent any 

  stages { 

     
stage('Build with unit testing') {
            steps {
                // Run the maven build
                script {
                    // Get the Maven tool.
                    // ** NOTE: This 'M3' Maven tool must be configured
                    // **       in the global configuration.
                    echo 'Pulling...' + env.BRANCH_NAME
                    def mvnHome = tool 'MAVEN_HOME'
					if (isUnix()) {
                        def targetVersion = getDevVersion()
                        print 'target build version...'
                        print targetVersion
                        sh "'${mvnHome}/bin/mvn' -Dintegration-tests.skip=true -Dbuild.number=${targetVersion} clean package"
                        def pom = readMavenPom file: 'pom.xml'
                        // get the current development version
                       // developmentArtifactVersion = "${pom.version}-${targetVersion}"
                        print pom.version
                        // execute the unit testing and collect the reports
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    }else {
                        bat(/"${mvnHome}\bin\mvn"  clean package/)
                        def pom = readMavenPom file: 'pom.xml'
						print '77777777777777777777777777777777777777777'
                        print pom.version
                        junit '**//*target/surefire-reports/TEST-*.xml'
                        archive 'target*//*.jar'
                    }
                }

            }
        }
        stage('install') {
            // Run integration test
            steps {
                script {
                    def mvnHome = tool 'MAVEN_HOME'
                    if (isUnix()) {
                        // just to trigger the integration test without unit testing
                        sh "'${mvnHome}/bin/mvn'  verify -Dunit-tests.skip=true"
                    } else {
                        bat(/"${mvnHome}\bin\mvn" clean install/)
                    }

                }
                // cucumber reports collection
               // cucumber buildStatus: null, fileIncludePattern: '**/cucumber.json', jsonReportDirectory: 'target', sortingMethod: 'ALPHABETICAL'
            }
    
        } 
stage('Development deploy approval and deployment') {
            steps {
                script {
                    if (currentBuild.result == null || currentBuild.result == 'SUCCESS') {
                        timeout(time: 3, unit: 'MINUTES') {
                            // you can use the commented line if u have specific user group who CAN ONLY approve
                            //input message:'Approve deployment?', submitter: 'it-ops'
                            input message: 'Approve deployment?'
                        }
                        timeout(time: 2, unit: 'MINUTES') {
                            //
						
						   print 'ggggggggggggggggggggg'  
                             

                        }
                    }
                }
            }
        }		
		
	
      stage('Building our image') { 

          steps { 

              script { 

                  dockerImage = docker.build registry + ":$BUILD_NUMBER" 

              }

          } 

      }

      stage('Deploy our image') { 

          steps { 

              script { 

                  docker.withRegistry( '', registryCredential ) { 

                      dockerImage.push() 

                  }

              } 

          }

      } 

      stage('Cleaning up') { 

          steps { 

              sh "docker rmi $registry:$BUILD_NUMBER" 

          }

      } 

  }

}
 
