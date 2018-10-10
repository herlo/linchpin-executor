/* This job builds the linchpin-executor container based upon 
 * config/dockerfiles/linchpin-executor within
 * https://github.com/CentOS-PaaS-SIG/contra-env-sample-project
 *
 * This Jenkinsfile will be run after a buld of the linchpin container is complete,
 */

import com.cloudbees.plugins.credentials.Credentials

env.IMAGE_NAME = env.IMAGE_NAME ?: 'linchpin-executor'
DOCKER_REPO_URL = env.DOCKER_REPO_URL ?: '172.30.254.79:5000'

properties([
  buildDiscarder(logRotator(artifactDaysToKeepStr: '10', artifactNumToKeepStr: '10', daysToKeepStr: '10', numToKeepStr: '10')),
  [$class: 'GithubProjectProperty', displayName: ''],
  disableConcurrentBuilds(),
  parameters(
    [
        string(defaultValue: 'herlo',
               description: 'Namespace where container will reside',
               name: 'CONTAINER_NAMESPACE'),
        string(description: 'Container Registry',
               name: 'CONTAINER_REGISTRY'),
        string(description: 'Container Version to build from upstream (contrainfra/linchpin)',
               name: 'CONTAINER_VERSION'),
    ]
  ),
])

def test_cmd = "uid_entrypoint run"

def containers = ['buildah']
def build_root = "config/Dockerfiles/${env.IMAGE_NAME}"
def credentials = [usernamePassword(credentialsId: 'linchpin-docker',
                   usernameVariable: 'CONTAINER_USERNAME', passwordVariable: 'CONTAINER_PASSWORD')]

podTemplate = [containers: containers,
               docker_repo_url: DOCKER_REPO_URL,
               podName: 'linchpin-executor-buildah']

env.CONTAINER_VERSION = 'latest'
def container_versions = []
if (CONTAINER_VERSION) {
    container_versions.add(CONTAINER_VERSION)
    env.CONTAINER_VERSION = CONTAINER_VERSION
}

deployOpenShiftTemplate(podTemplate) {

    // set the REPO_URL and REPO_REF for the Makefile
    def scmVars = checkout scm
    env.REPO_URL = scmVars.GIT_URL
    env.REPO_REF = scmVars.GIT_BRANCH

    ciPipeline(sendMetrics: false, decorateBuild: decoratePRBuild()) {
        buildTestContainer(versions: container_versions,
                           image_name: env.IMAGE_NAME,
                           container_registry: CONTAINER_REGISTRY,
                           container_namespace: CONTAINER_NAMESPACE,
                           credentials: credentials,
                           build_root: build_root)
    }
}
