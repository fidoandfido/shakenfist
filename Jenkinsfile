pipeline {
  agent {
    node {
      label 'sf-ci-image'
    }
  }

  stages {
    stage('Localhost deployment') {
      steps {
        sh '''  echo "Updating worker"
                sudo apt-get update
                sudo apt-get -y dist-upgrade
                sudo apt-get -y install tox ansible pwgen build-essential python3-dev python3-wheel python3-pip curl
                ansible-galaxy install andrewrothstein.etcd-cluster andrewrothstein.terraform andrewrothstein.go

                echo "Deploying on localhost"
                cd $WORKSPACE/deploy/ansible
                CLOUD=localhost RELEASE="git:master" ./deployandtest.sh
          '''
        }
      }
   stage('Run CI tests') {
     steps {
        sh '''  # Run the nextgen CI (the old CI wont work on single node deployments)
                #cd $WORKSPACE/deploy
                #sudo chmod ugo+rx /etc/sf/shakenfist.json
                #tox -epy3
          '''
      }
    }
    stage('Assert that the logs look sensible') {
      steps {
        sh '''  # Ensure we don't have any tracebacks
                if [ `grep -c "Traceback (most recent call last):" /var/log/syslog` -gt 0 ]
                then
                  echo "We have tracebacks in the logs!"
                  exit 1
                fi

                # Ensure nothing died
                if [ `grep -c "died"` -gt 0 ]
                then
                  echo "A process died!"
                  exit 1
                fi
          '''
      }
    }
  }

  post {
    always {
      sh '''  cat /var/log/syslog'''
      }
    }
  }