podTemplate(label: 'dind', containers: [
  containerTemplate(name: 'docker-dind', image: 'docker:stable-dind', privileged: true, command: '', args: ''),
  containerTemplate(name: 'docker-build', image: 'docker:latest', ttyEnabled: true, command: '/bin/sh -c', args: 'cat', envVars: [
      containerEnvVar(key: 'DOCKER_HOST', value: 'tcp://localhost:2375'),
      ]),
  ]) {

  node('dind') {
    stage('Build java docker container') {
      git 'https://github.com/ibelikov/alpine-oracle-java-8'
      container('docker-build') {
          sh 'docker build -t alpine-oracle-java-8 .'
      }
    }
    stage('Run java -version in built container') {
      container('docker-build') {
          sh "docker run alpine-oracle-java-8 /bin/sh -c 'java -version'"
      }
    }
  }
}
