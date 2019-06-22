def CONTRAIL_VERSION="R5.1"
def BRANCH="R5.1"
def image_name="tungstenfabric/developer-sandbox"
def image_tag="R5.1"
def BUILD_FOLDER="contrail-env"
pipeline {
        agent any
        stages {
                stage ("SCM CHECKOUT") {
                        steps {
							sh label: '',
							script: "mkdir -p ${BUILD_FOLDER}"
							dir ("${BUILD_FOLDER}"){
                                git branch: "${BRANCH}", url: 'https://github.com/Juniper/contrail-dev-env.git'
								}
                        }
        }
        stage ("SandBox Creation") {
                        steps {
                                sh label: '',
                                script: "cd ${BUILD_FOLDER}/container; \
                                cp ../tpc.repo.template tpc.repo; \
                                docker build --build-arg BRANCH=${BRANCH} \
                                --no-cache --tag ${image_name}:${image_tag} ."
                        }
        }
                stage ("Container Creation") {
                        steps {
                                sh label: '',
                                script: "cd ${WORKSPACE}/${BUILD_FOLDER}; ./startup.sh -i ${image_name} -t ${image_tag}"
                        }
        }
}
