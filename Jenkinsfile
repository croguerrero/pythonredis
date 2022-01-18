node {
    def app

    stage('Clone repository') {      

        checkout scm
    }

    stage('Build image') {
  
       app = docker.build("croguerrero/pytest") //insert your docker image name here
    }

    stage('Test image') {
  

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            app.push("${env.BUILD_NUMBER}")
        }
    }
    
    
    stage('Update GIT') {
            script {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withCredentials([usernamePassword(credentialsId: 'githubk', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        //def encodedPassword = URLEncoder.encode("$GIT_PASSWORD",'UTF-8')
                        sh "git config user.email christianmarcelog@gmail.com"
                        sh "git config user.name croguerrero"
                        //sh "git switch master"
                        sh "cat pythonredis-deployment.yaml"
                        sh "sed -i 's+croguerrero/pytest.*+croguerrero/pytest:${DOCKERTAG}+g' pythonredis-deployment.yaml"
                        sh "cat pythonredis-deployment.yaml"
                        sh "git add ."
                        sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                        sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/pythonredis.git HEAD:main"
      }
    }
  }
 }
}