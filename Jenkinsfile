pipeline {
  agent any
  stages {
    stage('Build') {
        agent {
          kubernetes {
            label 'kaniko'
            idleMinutes 5
            yamlFile 'kaniko-pod.yaml'
            defaultContainer 'kaniko'
          }
        }
        steps {
          git 'https://github.com/rkamradt/myfirstrepository'
          container(name: 'kaniko') {
            sh "/kaniko/executor \
                --dockerfile `pwd`/Dockerfile \
                --context `pwd` \
                --destination=docker.io/rlkamradt/myfirstrepository:latest"
            sh "/kaniko/executor \
                --dockerfile `pwd`/test/Dockerfile \
                --context `pwd`/test \
                --destination=docker.io/rlkamradt/myfirstrepositorytest:latest"
          }
        }
    }
    stage('Test') {
      agent {
        kubernetes {
          label 'kaniko'
          idleMinutes 5
          yamlFile 'kaniko-pod.yaml'
          defaultContainer 'kaniko'
        }
      }
      steps {
        container(name: 'kubectl') {
          sh "kubectl apply -n test -f mongodb.yaml"
          sh "kubectl delete -n test --ignore-not-found=true -f myfirstrepository.yaml"
          sh "kubectl apply -n test -f myfirstrepository.yaml"
        }
      }
    }
    stage('Deploy') {
      steps {
          echo 'Deploying'
      }
    }
  }
}
