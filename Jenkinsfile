#!/usr/bin/env groovy

def podDefinition = """
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: iac
    image: 'ECR-URL/hcm/iac-tools:v1.0.2'
    tty: true
    command:
    - 'cat'
"""

podTemplate(yaml: podDefinition) {
  node(POD_LABEL) {
    timestamps {
      ansiColor('xterm') {
        container("iac") {
          stage('Setup environment') {
            deleteDir()
            checkout scm
          }

          try {
            withCredentials([usernamePassword(credentialsId: 'domainconnection', passwordVariable: 'Password', usernameVariable: 'Username'), string(credentialsId: 'domain_safe_mode_password', variable: 'Secret'), [$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', credentialsId: 'AWS_HCMWIN_DC', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
             ansiblePlaybook(
                 playbook: 'test.yml',
                 inventory: 'hosts',
                 installation: 'ansible-latest',
                 disableHostKeyChecking: true,
                 become: 'yes',
                 extras: '-vvv',
                 extraVars: [
                 domain_admin: "${Username}",
                 domain_admin_password: "${Password}",
                 domain_safemode_password: "${Secret}"]
                )
            }
          } catch (err) {
            throw (err)
          }
        }
      }
    }
  }
}
