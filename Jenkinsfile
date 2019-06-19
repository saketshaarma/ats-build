def CONTRAIL_VERSION="r5.1"
def BRANCH="R5.1"
image_name="tungstenfabric/developer-sandbox"
image_tag="r5.1"
pipeline {
	agent any
	stages {
		stage ("SCM CHECKOUT") {
			steps {
				git branch: "${BRANCH}", url: 'https://github.com/Juniper/contrail-dev-env.git'
			}
	}
	stage ("SandBox Creation") {
			steps {
				sh label: '',
				script: "cd container; \
				cp ../tpc.repo.template tpc.repo; \
				docker build --build-arg BRANCH=${BRANCH} \
				--no-cache --tag ${image_name}:${image_tag} ."
			}
	}
		stage ("Container Creation") {
			steps {
				sh label: '',
				script: "./startup.sh -i ${image_name} -t ${image_tag}"
			}
	}
	
		stage ("Creating tpc.repo.template") {
			steps {
				sh label: '',
				script: '''cat <<EOF> tpc.repo.template
				[contrail-tpc]
				name=Third partiey for Contrail
                bayeurl=http://148.251.5.90/tpc-R5.1/
                enabled=1
                gpgcheck=0
                EOF'''
			}
	}
		stage ("Copying tpc.repo.template to container") {
			steps {
				sh label: '',
				script: "docker cp tpc.repo.template contrail-developer-sandbox:/root/contrail-dev-env"
			}
	}
	
		stage ("Building RPMs") {
			steps {
				sh label: '',
				script: 'docker exec  contrail-developer-sandbox bash -c \'set -x; cd /root/contrail-dev-env; make sync; make fetch_packages; make setup; make dep; export SRCVER=r5.1; export BUILDTAG=1; make rpm; export CONTRAIL_CONTAINER_TAG="${CONTRAIL_VERSION}"; sed -i "/CONTRAIL_VERSION/d" common.env; export SB_BRANCH="${BRANCH}"; make containers; export SB_BRANCH="${BRANCH}"; make deployers\''
			}
	}
	
		stage ("Creating Hasb manifest") {
			steps {
				sh label: '',
				script: "docker exec  contrail-developer-sandbox bash -c \'set -x; cd /root/contrail; repo manifest -r -o /root/contrail-dev-env/manifest_"$(date +"%H:%M-%Y-%m-%d")".xml\'"
			}
	}
	
	}
}
