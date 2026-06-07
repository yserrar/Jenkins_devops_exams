pipeline {
    environment {
        DOCKER_ID       = "yserrar"
        DOCKER_TAG      = "v.${BUILD_ID}.0"
        DOCKER_CAST     = "cast-service"
        DOCKER_MOVIE    = "movie-service"
    }
    agent any

    stages {

        stage('Docker Build') {
            steps {
                script {
                    sh '''
                        docker rm -f cast-service movie-service || true
                        docker build -t $DOCKER_ID/$DOCKER_CAST:$DOCKER_TAG ./cast-service
                        docker build -t $DOCKER_ID/$DOCKER_MOVIE:$DOCKER_TAG ./movie-service
                    '''
                }
            }
        }

        stage('Docker Push') {
            environment {
                DOCKER_PASS = credentials("DOCKER_HUB_PASS")
            }
            steps {
                script {
                    sh '''
                        docker login -u $DOCKER_ID -p $DOCKER_PASS
                        docker push $DOCKER_ID/$DOCKER_CAST:$DOCKER_TAG
                        docker push $DOCKER_ID/$DOCKER_MOVIE:$DOCKER_TAG
                    '''
                }
            }
        }

        stage('Deploiement en dev') {
            environment {
                KUBECONFIG = credentials("configid")
                NAMESPACE  = "dev"
            }
            steps {
                script {
                    sh '''
                        rm -Rf .kube && mkdir .kube
                        cat $KUBECONFIG > .kube/config

                        # cast-service
                        cp charts/values.yaml values-cast.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_CAST+g" values-cast.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-cast.yml
                        sed -i "s+nodePort:.*+nodePort: 30001+g" values-cast.yml
                        sed -i "s|uri:.*|uri: \"postgresql://cast_db_username:cast_db_password@cast-service-fastapiapp-db/cast_db_dev\"|g" values-cast.yml
                        sed -i "s|user:.*|user: \"cast_db_username\"|g" values-cast.yml
                        sed -i "s|password:.*|password: \"cast_db_password\"|g" values-cast.yml
                        sed -i "s|name:.*|name: \"cast_db_dev\"|g" values-cast.yml
                        helm upgrade --install cast-service charts --values=values-cast.yml --namespace $NAMESPACE --create-namespace

                        # movie-service
                        cp charts/values.yaml values-movie.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_MOVIE+g" values-movie.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-movie.yml
                        sed -i "s+nodePort:.*+nodePort: 30005+g" values-movie.yml
                        sed -i "s|uri:.*|uri: \"postgresql://movie_db_username:movie_db_password@movie-service-fastapiapp-db/movie_db_dev\"|g" values-movie.yml
                        sed -i "s|user:.*|user: \"movie_db_username\"|g" values-movie.yml
                        sed -i "s|password:.*|password: \"movie_db_password\"|g" values-movie.yml
                        sed -i "s|name:.*|name: \"movie_db_dev\"|g" values-movie.yml
                        helm upgrade --install movie-service charts --values=values-movie.yml --namespace $NAMESPACE --create-namespace
                    '''
                }
            }
        }

        stage('Deploiement en QA') {
            environment {
                KUBECONFIG = credentials("configid")
                NAMESPACE  = "qa"
            }
            steps {
                script {
                    sh '''
                        rm -Rf .kube && mkdir .kube
                        cat $KUBECONFIG > .kube/config

                        cp charts/values.yaml values-cast.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_CAST+g" values-cast.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-cast.yml
                        sed -i "s+nodePort:.*+nodePort: 30002+g" values-cast.yml
                        sed -i "s|uri:.*|uri: \"postgresql://cast_db_username:cast_db_password@cast-service-fastapiapp-db/cast_db_dev\"|g" values-cast.yml
                        sed -i "s|user:.*|user: \"cast_db_username\"|g" values-cast.yml
                        sed -i "s|password:.*|password: \"cast_db_password\"|g" values-cast.yml
                        sed -i "s|name:.*|name: \"cast_db_dev\"|g" values-cast.yml
                        helm upgrade --install cast-service charts --values=values-cast.yml --namespace $NAMESPACE --create-namespace

                        cp charts/values.yaml values-movie.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_MOVIE+g" values-movie.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-movie.yml
                        sed -i "s+nodePort:.*+nodePort: 30006+g" values-movie.yml
                        sed -i "s|uri:.*|uri: \"postgresql://movie_db_username:movie_db_password@movie-service-fastapiapp-db/movie_db_dev\"|g" values-movie.yml
                        sed -i "s|user:.*|user: \"movie_db_username\"|g" values-movie.yml
                        sed -i "s|password:.*|password: \"movie_db_password\"|g" values-movie.yml
                        sed -i "s|name:.*|name: \"movie_db_dev\"|g" values-movie.yml
                        helm upgrade --install movie-service charts --values=values-movie.yml --namespace $NAMESPACE --create-namespace
                    '''
                }
            }
        }

        stage('Deploiement en staging') {
            environment {
                KUBECONFIG = credentials("configid")
                NAMESPACE  = "staging"
            }
            steps {
                script {
                    sh '''
                        rm -Rf .kube && mkdir .kube
                        cat $KUBECONFIG > .kube/config

                        cp charts/values.yaml values-cast.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_CAST+g" values-cast.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-cast.yml
                        sed -i "s+nodePort:.*+nodePort: 30003+g" values-cast.yml
                        sed -i "s|uri:.*|uri: \"postgresql://cast_db_username:cast_db_password@cast-service-fastapiapp-db/cast_db_dev\"|g" values-cast.yml
                        sed -i "s|user:.*|user: \"cast_db_username\"|g" values-cast.yml
                        sed -i "s|password:.*|password: \"cast_db_password\"|g" values-cast.yml
                        sed -i "s|name:.*|name: \"cast_db_dev\"|g" values-cast.yml
                        helm upgrade --install cast-service charts --values=values-cast.yml --namespace $NAMESPACE --create-namespace

                        cp charts/values.yaml values-movie.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_MOVIE+g" values-movie.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-movie.yml
                        sed -i "s+nodePort:.*+nodePort: 30007+g" values-movie.yml
                        sed -i "s|uri:.*|uri: \"postgresql://movie_db_username:movie_db_password@movie-service-fastapiapp-db/movie_db_dev\"|g" values-movie.yml
                        sed -i "s|user:.*|user: \"movie_db_username\"|g" values-movie.yml
                        sed -i "s|password:.*|password: \"movie_db_password\"|g" values-movie.yml
                        sed -i "s|name:.*|name: \"movie_db_dev\"|g" values-movie.yml
                        helm upgrade --install movie-service charts --values=values-movie.yml --namespace $NAMESPACE --create-namespace
                    '''
                }
            }
        }

        stage('Deploiement en prod') {
            when {
                anyOf {
                    branch 'master'
                    expression { env.GIT_BRANCH == 'origin/master' }
                }
            }
            environment {
                KUBECONFIG = credentials("configid")
                NAMESPACE  = "prod"
            }
            steps {
                timeout(time: 15, unit: "MINUTES") {
                    input message: 'Déployer en production ?', ok: 'Yes'
                }
                script {
                    sh '''
                        rm -Rf .kube && mkdir .kube
                        cat $KUBECONFIG > .kube/config

                        cp charts/values.yaml values-cast.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_CAST+g" values-cast.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-cast.yml
                        sed -i "s+nodePort:.*+nodePort: 30004+g" values-cast.yml
                        sed -i "s|uri:.*|uri: \"postgresql://cast_db_username:cast_db_password@cast-service-fastapiapp-db/cast_db_dev\"|g" values-cast.yml
                        sed -i "s|user:.*|user: \"cast_db_username\"|g" values-cast.yml
                        sed -i "s|password:.*|password: \"cast_db_password\"|g" values-cast.yml
                        sed -i "s|name:.*|name: \"cast_db_dev\"|g" values-cast.yml
                        helm upgrade --install cast-service charts --values=values-cast.yml --namespace $NAMESPACE --create-namespace

                        cp charts/values.yaml values-movie.yml
                        sed -i "s+repository:.*+repository: $DOCKER_ID/$DOCKER_MOVIE+g" values-movie.yml
                        sed -i "s+tag:.*+tag: $DOCKER_TAG+g" values-movie.yml
                        sed -i "s+nodePort:.*+nodePort: 30008+g" values-movie.yml
                        sed -i "s|uri:.*|uri: \"postgresql://movie_db_username:movie_db_password@movie-service-fastapiapp-db/movie_db_dev\"|g" values-movie.yml
                        sed -i "s|user:.*|user: \"movie_db_username\"|g" values-movie.yml
                        sed -i "s|password:.*|password: \"movie_db_password\"|g" values-movie.yml
                        sed -i "s|name:.*|name: \"movie_db_dev\"|g" values-movie.yml
                        helm upgrade --install movie-service charts --values=values-movie.yml --namespace $NAMESPACE --create-namespace
                    '''
                }
            }
        }
    }

    post {
        failure {
            echo "Pipeline échoué"
            //mail to: "yserrar@email.com",
            //     subject: "${env.JOB_NAME} - Build #${env.BUILD_ID} failed",
            //     body: "Console: ${env.BUILD_URL}"
        }
    }
}