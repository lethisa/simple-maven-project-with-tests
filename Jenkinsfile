def label = "mypod-${UUID.randomUUID().toString()}"
podTemplate(label: label, 
  serviceAccount: 'jenkins-server',
  containers: [
    containerTemplate(
      name: 'docker', 
      image: 'docker:17.12.1-ce',
      ttyEnabled: true, 
      command: 'cat',
      envVars: [
        envVar(key: 'DOCKER_HOST', value: 'tcp://dind:2375')
      ]
    ),
    containerTemplate(
      name: 'helm', 
      image: 'lachlanevenson/k8s-helm:v2.8.1', 
      ttyEnabled: true, 
      command: 'cat'
    )
  ],
  volumes: [
    emptyDirVolume(mountPath: '/var/lib/docker', memory: false)
  ]) {

    node(label) {
        checkout scm
        stage('Build') {
          withMaven(maven: 'M3') {
            if (isUnix()) {
              sh 'mvn -Dmaven.test.failure.ignore clean package'
            }
            else {
              bat 'mvn -Dmaven.test.failure.ignore clean package'
            }
          }
        }

      stage('Results') {
        junit '**/target/surefire-reports/TEST-*.xml'
        archive 'target/*.jar'
      }
    }
  }

