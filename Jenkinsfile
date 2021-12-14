library identifier: "pipeline-library@v1.5",
retriever: modernSCM(
  [
    $class: "GitSCMSource",
    remote: "https://github.com/redhat-cop/pipeline-library.git"
  ]
)

// OKD의 Buildconifg의 이름과 일치시켜야 한다. 
appName = "okd-tutorial"

pipeline {
  agent {
    node {
      label 'nodejs' 
    }
  }
  options {
    timeout(time: 20, unit: 'MINUTES') 
  }
  stages {
    
    stage("Update Tag") { 
      steps {
        checkout([$class: 'GitSCM',
                        branches: [[name: '*/master' ]],
                        extensions: scm.extensions,
                        userRemoteConfigs: [[
                            url: 'git@github.com:blackwhale-testuser/okd-tutorial1-gitops.git',
                            credentialsId: 'jenkins-ssh-private',
                        ]]
                ])
        sshagent(credentials: ['jenkins-ssh-private']){
            sh("""
                #!/usr/bin/env bash
                set +x
                export GIT_SSH_COMMAND="ssh -oStrictHostKeyChecking=no"
                echo Build Number = {$BUILD_NUMBER}
                git config --global user.email "test@gmail.com"
                git checkout master
                cp --f base/deployment-sample.yaml okd-deploy/testblog-deployment.yaml
                cd okd-deploy
                sed -i 's/MY_BUILD_TAG/$BUILD_NUMBER/' testblog-deployment.yaml 
                cat testblog-deployment.yaml 
                git commit -a -m "demo tag"
                git push
            """)
        }
       }
    }
  }
} 
