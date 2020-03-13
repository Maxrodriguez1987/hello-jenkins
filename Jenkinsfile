pipeline {
  agent {
     label "maven"
    }
  options {
    skipDefoultCheckout()
    disableConcurrentBuilds()
  }
  stages { 
     stage('Checkout') {
       steps {
          checkout(scm)
        }
      }
    }  
  stages { 
     stage('Compile') {
       steps {
         sh "mvn package --DskipTest"
        }
      }
    }
  stages { 
     stage('Test') {
       steps {
          sh "mvn test"
        }
     }
    }
  stages { 
      stage('Build Image') {
        steps {
          script{
            openshift.withCluster {
              openshift.withProject{
                openshift.selector("bc", "hello-openshift").startBuild("--form-dir=./target","--wait=true")
              }
            }
          }  
        }
      }
    }
  stages { 
      stage('Deploy') {
        steps {
          script{
            openshift.withCluster {
              openshift.withProject{
                //remuevo todos los triggers
                openshift.set("triggers", "dc/hello-openshift", "--remove-all")
                //creo mi nuevo trigger del tipo from image que automaticamente me produce un deploy
                openshift.set("triggers", "dc/hello-openshift", "--from-image=hello-openshift:latest", "-c hello-openshift")
                openshift.selector("dc", "hello-openshift").rollout().status()
              }
            }
          }  
        }
      }
    }          
  }
