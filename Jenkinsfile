pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: 'github',
                url: 'https://github.com/huzaifadevcloud/Deploy-Todo-App-Using-Jenkins-and-ArgoCD',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t huzaifafev/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
            
            steps{
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_TOKEN')]) {
                    sh '''
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_TOKEN
                    docker push $DOCKERHUB_USERNAME/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: 'github',
                url: 'https://github.com/huzaifadevcloud/cicd-manifests',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([
                    
                        usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN'),
                        string(credentialsId: 'git-user-name', variable: 'GIT_USER_NAME'),
                        string(credentialsId: 'git-user-email', variable: 'GIT_USER_EMAIL')
                    
                    
                    ]) 
                    
                    
                    
                    {
                        sh '''
                        cat deploy.yaml
                        sed -i 's/32/${BUILD_NUMBER}/g' deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git config --global user.name "$GIT_USER_NAME"
                        git config --global user.email "$GIT_USER_EMAIL"
                        # Now your git commit will use these values
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/huzaifadevcloud/cicd-manifests HEAD:main
                        '''
                    }
                                            
                }
            }
        }
    }
}
