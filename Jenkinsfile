properties(
    [
        buildDiscarder(
            logRotator(
                daysToKeepStr: '5',
                numToKeepStr: '5'
            )
        )
    ]
)



podTemplate(containers: [
    containerTemplate(name: 'gradle', image: 'gradle:6.5.1-jdk11', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', command: 'cat', ttyEnabled: true),
    containerTemplate(name: 'node', image: 'node:14.17.0', command: 'cat', ttyEnabled: true, resourceRequestMemory: '250Mi', resourceLimitMemory: '1.5Gi', resourceRequestCpu: '500m', resourceLimitCpu: '1'),
    containerTemplate(name: 'docker', image: 'docker', command: 'cat', ttyEnabled: true, resourceRequestMemory: '250Mi', resourceLimitMemory: '1.5Gi', resourceRequestCpu: '500m', resourceLimitCpu: '1')
],

volumes: [
    hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock')
]
)
{
node(POD_LABEL) {
        stage('Checkout') {
            git "https://github.com/Ramananaidu/Kubernetes-DokcerImage-Push-ACR.git" , branch: "master"
            container('docker') {
stage('Build') {
    sh """
    docker build --network=host -t "mt-client-app":develop.v1."${BUILD_NUMBER}" .
    """
}
}
def ACR_NAME = 'myazurerc1'
def ACR_LOCATION = 'eastus'

container('docker') {
stage('image push to ECR') {
    withDockerRegistry('https://${ACR_NAME}.io','${AZURE_REGION}')
    {
        sh "docker tag mt-client-app:develop.v1.${BUILD_NUMBER} ${ACR_NAME}:prod.v1.${BUILD_NUMBER}"
        sh "docker push ${ACR_NAME}:prod.v1.${BUILD_NUMBER}"
    }
}
}
}
}
