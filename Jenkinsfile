pipeline {
  agent {
    kubernetes {
      label 'nginx'
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  serviceAccountName: cd-jenkins
  containers:
  - name: centos
    image: centos:latest
    command:
    - cat
    tty: true
    volumeMounts:
      - mountPath: "/root"
        name: root
  - name: docker
    image: docker:latest
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  volumes:
    - name: docker-sock
      hostPath:
        path: /var/run/docker.sock
    - name: root
      persistentVolumeClaim:
        claimName: root
"""
}
   }
  stages {
    stage('Build') {
      steps {
        container('centos') {
          sh """
                        cat /etc/*release
                                                """
        }
      }
    }
    stage('Test') {
      steps {
        container('centos') {
          sh """
             mvn test
          """
        }
      }
    }
    stage('Push') {
      steps {
        container('docker') {
          sh """
             docker build -t nginx:$BUILD_NUMBER .
          """
        }
      }
    }
  }
}
