// Copyright (c) Open Enclave SDK contributors.
// Licensed under the MIT License.

OECI_LIB_VERSION = env.OECI_LIB_VERSION ?: "master"
oe = library("OpenEnclaveCommon@${OECI_LIB_VERSION}").jenkins.common.Openenclave.new()

GLOBAL_TIMEOUT_MINUTES = 240
OETOOLS_REPO = "https://oenc-jenkins.sclab.intel.com:5000"
OETOOLS_REPO_CREDENTIAL_ID = "oejenkinscidockerregistry"
OETOOLS_DOCKERHUB_REPO_CREDENTIAL_ID = "oeciteamdockerhub"

def buildDockerImages() {
    node("dockerNUC") {
        timeout(GLOBAL_TIMEOUT_MINUTES) {
            stage("Checkout") {
                cleanWs()
                checkout scm
            }
            String buildArgs = oe.dockerBuildArgs("UID=\$(id -u)", "UNAME=\$(id -un)",
                                                  "GID=\$(id -g)", "GNAME=\$(id -gn)")
            stage("Build Ubuntu 18.04 SGX1-LLC Full Docker Image") {
                echo "Current image: oetools-sgx1-llc-full-18.04:1.${currentBuild.number}"
                //oesgx1llcfull1804 = oe.dockerImage("oetools-sgx1-llc-full-18.04:1.${currentBuild.number}", ".jenkins/infrastructure/dockerfiles/Dockerfile.SGX1-LLC.full", "${buildArgs} --build-arg ubuntu_version=18.04")
            }
            
            stage("Build Ubuntu 18.04 Full Docker Image") {
                echo "current build number: ${currentBuild.number}"
                oefull1804 = oe.dockerImage("oetools-full-18.04:1.${currentBuild.number}", ".jenkins/infrastructure/dockerfiles/Dockerfile.full", "${buildArgs} --build-arg ubuntu_version=18.04")
            }
            stage("Push to OE Docker Registry") {
                docker.withRegistry(OETOOLS_REPO) {
                    oe.exec_with_retry { oefull1804.push() }
                    //oe.exec_with_retry { oesgx1llcfull1804.push() }
                    if(TAG_LATEST == "true") {
                        oe.exec_with_retry { oefull1804.push('latest') }
                       // oe.exec_with_retry { oesgx1llcfull1804.push('latest') }
                    }
                }
            }
        }
    }
}

buildDockerImages()
