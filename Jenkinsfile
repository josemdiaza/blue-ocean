podTemplate(yaml: """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  # serviceAccountName: cd-jenkins
  containers:
  - name: centos
    image: centos:latest
    command:
    - cat
    tty: true
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
"""
           ){
    node(POD_LABEL) {
    stage('Check release') {
        container('centos') {
          sh """
                        cat /etc/os-release
                                                """
        }
      }
    stage('Test') {
        container('centos') {
          sh """
             echo test
          """
        }
      }
    stage('Build') {
        git 'https://github.com/josemdiaza/blue-ocean.git'
        container('docker') {
          sh """
             docker build -t nginx:$BUILD_NUMBER .
          """
      }
    }
  }
}
