pipeline{
        agent any
        stages{
            stage('Build docker images on Jenkins'){
                steps{
                    script{
                        if (env.GIT_BRANCH == 'origin/dev') {
                            sh '''
                            docker build -t mtkg/triobase-nginx-img:latest ./nginx
                            docker build -t mtkg/triobase-flask-app-img:latest ./flask-app
                            docker build -t mtkg/triobase-db-img:latest ./db
                            '''
                        } else {
                            sh '''
                            echo "Build not required"
                            '''
                        }
                    }
                }
            }
            stage('Push images to Docker Hub'){
                steps{
                    script{
                        if (env.GIT_BRANCH == 'origin/dev') {
                            sh '''
                            docker push mtkg/triobase-nginx-img:latest
                            docker push mtkg/triobase-flask-app-img:latest
                            docker push mtkg/triobase-db-img:latest
                            '''
                        } else {
                            sh '''
                            echo "Push not required"
                            '''
                        }
                    }
                }
            }
            stage('Cleanup') {
                steps {
                    script{
                        if (env.GIT_BRANCH == 'origin/dev') {
                            sh '''
                            docker rmi mtkg/triobase-nginx-img:latest
                            docker rmi mtkg/triobase-flask-app-img:latest
                            docker rmi mtkg/triobase-db-img:latest
                            '''
                        } else {
                            sh '''
                            echo "Cleanup not required"
                            '''
                        }
                    }
                }
            }
            stage('Deploy - run a rollout restart'){
                steps{
                    script{
                        if (env.GIT_BRANCH == 'origin/dev') {
                            sh '''
                            kubectl apply -f ./k8s/flask.yaml -n development
                            kubectl apply -f ./k8s/mysql.yaml -n development
                            kubectl apply -f ./k8s/nginx.yaml -n development
                            kubectl rollout restart deployment flask-deployment -n development
                            '''
                        } else if (env.GIT_BRANCH == 'origin/main') {
                            sh '''
                            kubectl apply -f ./k8s/flask.yaml -n production
                            kubectl apply -f ./k8s/mysql.yaml -n production
                            kubectl apply -f ./k8s/nginx.yaml -n production
                            kubectl rollout restart deployment flask-deployment -n production
                            '''
                        } else {
                            sh '''
                            echo "Branch not recognised"
                            '''
                        }
                    }
                }
            }
        }
}