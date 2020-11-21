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
    Helm install elasticsearch-exporter application
*/
def helmDryrunElasticsearchExporter (ElasticsearchExporterReleaseName) {
    echo "Installing ElasticsearchExporter application"

    script {
       sh "/usr/local/bin/helm repo add prometheus-community https://prometheus-community.github.io/helm-charts"

       // sh "helm repo add helm ${HELM_REPO}; helm repo update"
       sh "/usr/local/bin/helm upgrade --install elasticsearch-exporter prometheus-community/prometheus-elasticsearch-exporter --namespace=monitoring --dry-run --debug"
    }
}

/*
    Helm install ElasticsearchExporter application
*/
def helmInstallElasticsearchExporter (ElasticsearchExporterReleaseName) {
    echo "Installing ElasticsearchExporter application"

    script {
       sh "/usr/local/bin/helm repo add prometheus-community https://prometheus-community.github.io/helm-charts"

       // sh "helm repo add helm ${HELM_REPO}; helm repo update"
       sh "/usr/local/bin/helm upgrade --install elasticsearch-exporter prometheus-community/prometheus-elasticsearch-exporter --namespace=monitoring"
    }
}

/*
    Helm dry-run AppPreReq application
*/
def helmDryrunAppPrereq () {
    echo "Installing AppPreReq application"

    script {
       // get dependency 
       sh "/usr/local/bin/helm dependency update app-prereq/"

       // "helm repo add helm ${HELM_REPO}; helm repo update dryrun"
       sh "/usr/local/bin/helm upgrade --install app-prereq app-prereq --namespace=monitoring --dry-run --debug"
    }
}

/*
    Helm install AppPreReq application
*/
def helmInstallAppPrereq () {
    echo "Installing AppPreReq application"

    script {
        // "helm repo add helm ${HELM_REPO}; helm repo update"
        sh "/usr/local/bin/helm upgrade --install app-prereq app-prereq --namespace=monitoring"
        
        // install testclient kafka pod
        sh "kubectl apply -f app-prereq/test.yml -o yaml "
    }
}

node {

     def ElasticsearchExporterReleaseName = "elasticsearch-exporter"


    stage('Clone repository') {
        /* Cloning the Repository to our Workspace */
        checkout scm


        sh "export aws_access_key_id=${env.awsKey}"
        sh "export aws_secret_access_key=${env.awsSecret}"
        sh "export aws_profile=${env.aws_profile}"
        sh "export aws_region=${env.aws_region}"
        sh "export KOPS_STATE_STORE=${env.S3BucketName}"
        sh "AWS_PROFILE=${env.aws_profile} AWS_ACCESS_KEY_ID=${env.awsKey} AWS_SECRET_ACCESS_KEY=${env.awsSecret} kops export kubecfg ${env.YOUR_CLUSTER_NAME} --state=${env.S3BucketName}"
        
    }

    try {
        stage ('helm test') {
                echo "$pwd"
                helmDryrunAppPrereq ()
            }

        stage('Deploy AppPrereq'){
                createNamespace('monitoring')            
                helmInstallAppPrereq ()
            } 

        stage ('helm test') {
                echo "$pwd"
                helmDryrunElasticsearchExporter (ElasticsearchExporterReleaseName)
            }

        stage('Deploy ElasticsearchExporter'){
                helmInstallElasticsearchExporter(ElasticsearchExporterReleaseName)
            }
   
        }
        catch (Exception err){
            err_msg = "Test had Exception(${err})"
            currentBuild.result = 'FAILURE'
            error "FAILED - Stopping build for Error(${err_msg})"
    }
}