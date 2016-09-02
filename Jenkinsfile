def user = "pivotalservices"
def cfUser = "${cf_username}"
def cfPassword = "${cf_password}"
def cfUrl =  "${cf_api}"
def cfOrg = "${cf_org}"
def cfSpace = "${cf_space}"
def cfDomain = "${cf_domain}"
def cfAppName = "${cf_app_name}"


def sonarUrl = "${sonar_url}"
def nexusUrl = "${nexus_url}"

// def registry = "localhost:5000"
def flow

stage 'compile'
node {
	git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
	flow = load 'ci/pipeline.groovy'
	flow.build()
}

stage 'test'
node {
	git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
	flow = load 'ci/pipeline.groovy'
	flow.clean_test()
}

// disable sonar for now

// stage 'publish-sonar-results'
// node {
//	flow.sonar(sonarUrl)
// }

stage 'deploy-to-development'
node {
	git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
	flow = load 'ci/pipeline.groovy'
	flow.push("${cfUrl}", "${cfUser}", "${cfPassword}", "${cfOrg}", "${cfSpace}", "${cfDomain}", "${cfAppName}")
}

stage 'run-tests-on-dev'
parallel(
	smokeTests: {
		node {
			git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
			flow = load 'ci/pipeline.groovy'
			flow.runSmokeTests("${cfUrl}", "${cfUser}", "${cfPassword}", "${cfOrg}", "${cfSpace}", "${cfDomain}", "${cfAppName}")
		}
	},
	acceptanceTests: {
		node {
			git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
			flow = load 'ci/pipeline.groovy'
			flow.runAcceptanceTests("${cfUrl}", "${cfUser}", "${cfPassword}", "${cfOrg}", "${cfSpace}", "${cfDomain}", "${cfAppName}")
		}
	}
)

/**
stage name: 'deploy-to-test', concurrency: 1
node {
	git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
	flow = load 'ci/pipeline.groovy'
	flow.pushIf('api.run.pez.pivotal.io', "${cfUser}", "${cfPassword}", 'pivot-bkunjummen', 'test', 'cfapps.pez.pivotal.io', 'sample-spring-cloud-svc-ci-test')
}

stage 'run-tests-on-test'
parallel(
	smokeTests: {
		node {
			git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
			flow = load 'ci/pipeline.groovy'
			flow.runSmokeTests('api.run.pez.pivotal.io', "${cfUser}", "${cfPassword}", 'pivot-bkunjummen', 'test', 'cfapps.pez.pivotal.io', 'sample-spring-cloud-svc-ci-test')
		}
	},
	acceptanceTests: {
		node {
			git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
			flow = load 'ci/pipeline.groovy'
			flow.runAcceptanceTests('api.run.pez.pivotal.io', "${cfUser}", "${cfPassword}", 'pivot-bkunjummen', 'test', 'cfapps.pez.pivotal.io', 'sample-spring-cloud-svc-ci-test')
		}
	}
)
**/

input message: "Does https://sample-spring-cloud-svc-ci-prod.cfapps.pez.pivotal.io look good?"
try {
    checkpoint('Before production')
} catch (NoSuchMethodError _) {
    echo 'Checkpoint feature available in CloudBees Jenkins Enterprise.'
}

stage name: 'deploy-to-prod', concurrency: 1
node {
	git([url: "https://github.com/${user}/sample-spring-cloud-svc-ci.git", branch: 'jenkins'])
	flow = load 'ci/pipeline.groovy'
	flow.pushIf('api.run.pez.pivotal.io', "${cfUser}", "${cfPassword}", 'pivot-bkunjummen', 'prod', 'cfapps.pez.pivotal.io', 'sample-spring-cloud-svc-ci-prod')
}
