/*
    This is an example pipeline that implement full CI/CD for a simple static web site packed in a Docker image.
    The pipeline is made up of 6 main steps
    1. Git clone and setup
    2. Build and local tests
    3. Publish Docker and Helm
    4. Deploy to dev and test
    5. Deploy to staging and test
    6. Optionally deploy to production and test
 */


/*
    Create the kubernetes namespace
 */
def createNamespace (name) {
    echo "Creating namespace ${name} if needed"
    sh "kubectl create namespace ${name} --dry-run -o yaml | kubectl apply -f -"
}

/*
    Helm install Zookeeper application
*/
def helmInstallZookeeper (zookeeperReleaseName) {
    echo "Installing zookeeper application"

    script {
       // sh "helm repo add helm ${HELM_REPO}; helm repo update"
       sh "helm upgrade --install ${zookeeperReleaseName} --namespace=api --set secret.awscred.aws_key=${env.awsKey},secret.awscred.secret_key=${env.awsSecret},secret.regcred.dockerconfigjson=${env.dockerString} --debug ./back-end/"
    }
}

/*
    Helm install Kafka application
*/
def helmInstallKafka (kafkaReleaseName, zookeeperReleaseName) {
    echo "Installing kafka application"

    script {
       sh "sleep 50"
       echo "Finding zookeeperip"
       zookeeperIp = sh(returnStdout: true, script: "kubectl describe services zookeeper-back-end --namespace=api | grep elb.amazonaws.com | grep LoadBalancer | awk '{print \$3}' | tr -d '\n'")
       echo "${zookeeperIp}"
       sh "helm upgrade --install ${kafkaReleaseName} --namespace=ui --set intiContainer.zookeeperDependencyEndpoint=${zookeeperReleaseName}-back-end.api.svc.cluster.local,configmap.zookeeperIp='http://${zookeeperIp}:3000',secret.regcred.dockerconfigjson=${env.dockerString} --debug ./front-end/"
    }
}

node {
     def zookeeperIp
     def changedFiles
     def zookeeperReleaseName = "zookeeper"
     def kafkaReleaseName = "kafka"
     def kubernetesCredentials = 'KubeCreds'

    stage('Clone repository') {
        /* Cloning the Repository to our Workspace */
                checkout scm
                sh 'git diff --name-only --diff-filter=ADMR @~..@ > output.txt'
                changedFiles = readFile 'output.txt'
                echo "Changed files - ${changedFiles}"
            }

    stage('Startup activities'){
    echo "${env.ServerUrl}"
    withKubeConfig([credentialsId: kubernetesCredentials,
                        serverUrl: "${env.ServerUrl}"
                        ]) {
          sh "kubectl cluster-info"
    }


     // Init helm client
     sh "helm init"
    }

         stage('Deploy zookeeper'){
            if (changedFiles?.trim().contains("back-end"))
             {
                echo "Deploying zookeeper"
                withKubeConfig([credentialsId: kubernetesCredentials,
                                 serverUrl: "${env.ServerUrl}"
                              ]) {
                       createNamespace('api')
                       helmInstallZookeeper(zookeeperReleaseName)
                }

            }else{
                echo "Nothing to deploy in zookeeper"
            }
          }

         stage('Deploy kafka'){
            if (changedFiles?.trim().contains("front-end"))
            {
              echo "Deploying kafka"
              withKubeConfig([credentialsId: kubernetesCredentials,
                                 serverUrl: "${env.ServerUrl}"
                              ]) {
               createNamespace('ui')
               helmInstallKafka(kafkaReleaseName, zookeeperReleaseName)
              }
            }
            else{
                echo "Nothing to deploy in kafka"
            }
         }

 }