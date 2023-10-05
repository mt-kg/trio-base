pipeline{
        agent any
        stages{
            stage('Build docker images on Jenkins'){
                steps{
                    sh '''
                    docker build -t mtkg/triobase-nginx-img:latest ./nginx
                    docker build -t mtkg/triobase-flask-app-img:latest ./flask-app
                    docker build -t mtkg/triobase-db-img:latest ./db
                    '''
                }
            }
            stage('Push images to Docker Hub'){
                steps{
                    sh '''
                    docker push mtkg/triobase-nginx-img:latest
                    docker push mtkg/triobase-flask-app-img:latest
                    docker push mtkg/triobase-db-img:latest
                    '''
                }
            }
            stage('Cleanup') {
                steps {
                    sh '''
                    docker rmi mtkg/triobase-nginx-img:latest
                    docker rmi mtkg/triobase-flask-app-img:latest
                    docker rmi mtkg/triobase-db-img:latest
                    '''
                }
            }
            stage('Run a rollout restart'){
                steps{
                    sh '''
                    kubectl apply -f ./k8s/flask.yaml
                    kubectl apply -f ./k8s/mysql.yaml
                    kubectl apply -f ./k8s/nginx.yaml
                    kubectl rollout restart deployment flask-deployment
                    '''
                }
            }
        }
}