pipeline {
            agent any

            environment {
            GITHUB_ORG = 'aggreys-org'
            CONTAINER_REGISTRY = "ghcr.io/${GITHUB_ORG}"
            CONTAINER_REGISTRY_URL="https://${CONTAINER_REGISTRY}"
            ARTIFACT_ID = readMavenPom().getArtifactId()
            JAR_NAME = "${ARTIFACT_ID}-${BUILD_NUMBER}"
            JAR_LOCATION ="target/${JAR_NAME}.jar"
            IMAGE_NAME = "${CONTAINER_REGISTRY}${ARTIFACT_ID}"
            IMAGE_TAG = "${IMAGE_NAME}:${BUILD_NUMBER}"

            }

            stages{
                    stage('Build Application'){

                       agent{
                              docker {
                                    image 'openjdk:17'
                                    reuseNode true
                              }
                      }

                        steps {
                                sh 'echo Performing Maven Build: ${ARTIFACT_ID}'
                                sh 'mvn -DjarName=${JAR_NAME} clean verify'

                        }

                    }

                    stage('Build Container Image'){
                                            steps {
                                                    sh 'echo Building a Container Image: ${IMAGE_NAME}'
                                                    sh 'docker build --build-arg JAR_FILE=${JAR_LOCATION} -t ${IMAGE_TAG} .'

                                            }

                    }

                    stage('Publishing Container Image'){
                                            steps {
                                                    sh 'echo Publishing Container Image: ${CONTAINER_REGISTRY}'

                                                    script{
                                                        docker.withRegistry("$CONTAINER_REGISTRY_URL","gitbub-token"){
                                                        sh 'docker push ${IMAGE_TAG}'
                                                        }
                                                    }

                                            }

                    }
            }
}