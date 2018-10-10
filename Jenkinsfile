/* This job builds the linchpin-executor container based upon 
 * config/dockerfiles/linchpin-executor within
 * https://github.com/CentOS-PaaS-SIG/contra-env-sample-project
 *
 * This Jenkinsfile will be run after a buld of the linchpin container is complete,
 */

import com.cloudbees.plugins.credentials.Credentials

env.GHRepository = env.GHRepository ?: 'herlo/linchpin-executor'

// Needed for podTemplate()
env.SLAVE_TAG = env.SLAVE_TAG ?: 'stable'

env.IMAGE_NAME = env.IMAGE_NAME ?: 'linchpin-executor'
DOCKER_REPO_URL = env.DOCKER_REPO_URL ?: '172.30.254.79:5000'

properties([
  buildDiscarder(logRotator(artifactDaysToKeepStr: '30', artifactNumToKeepStr: '15', daysToKeepStr: '90', numToKeepStr: '30')),
  [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/' + env.GHRepository],
  disableConcurrentBuilds(),
  parameters(
    [
        string(defaultValue: 'stable',
               description: 'Tag for slave image',
               name: 'SLAVE_TAG'),
        string(defaultValue: 'herlo',
               description: 'Namespace where container will reside',
               name: 'CONTAINER_NAMESPACE'),
        string(description: 'Container Registry',
               name: 'CONTAINER_REGISTRY'),
        string(description: 'Container Version',
               name: 'CONTAINER_VERSION'),
    ]
  ),
])

def test_cmd = "uid_entrypoint run"

def containers = ['buildah']
def build_root = "config/Dockerfiles/${env.IMAGE_NAME}"
def credentials = [usernamePassword(credentialsId: 'linchpin-docker',
                   usernameVariable: 'CONTAINER_USERNAME', passwordVariable: 'CONTAINER_PASSWORD')]

def release_version = null
container_version = CONTAINER_VERSION ?: null

def container_versions = ['latest']

if (container_version) {
    container_versions.add(version)
}

podTemplate = [containers: containers,
               docker_repo_url: DOCKER_REPO_URL,
               podName: 'linchpin-executor-buildah']


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
