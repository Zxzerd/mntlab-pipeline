#!/usr/bin/env groovy
 

node (env.SLAVE) {

	env.PATH=env.PATH+":/opt/gradle/bin"

stage '\u2776 Preparation (Checking out)'
	
	git url: "https://github.com/MNT-Lab/mntlab-pipeline.git", branch: 'atsuranau'
	echo "\u2600 Repo downloaded"
 

 
stage '\u2777 Building'
	
	sh 'gradle build'
	wrap([$class: 'TimestamperBuildWrapper']) {
     	echo "\u2600 Build completed"
   }

stage '\u2778 Testing'

	parallel (
		'Cucumber Tests': {sh 'gradle cucumber' },
                'Jacoco Tests': {sh 'gradle jacocoTestReport' },
                'Unit Tests': {sh 'gradle test' }
		)
	wrap([$class: 'TimestamperBuildWrapper']) {
     	echo "\u2600 Testing completed"
   }

stage '\u2779 Triggering job and fetching artifacts'

	build job: 'EPBYMINW2629/MNTLAB-atsuranau-child1-build-job', parameters: [string(name: 'BRANCH_NAME', value: 'atsuranau')], wait: true
	step([$class: 'CopyArtifact', 
		filter: 'atsuranau_dsl_script.tar.gz', 
		fingerprintArtifacts: true, 
		flatten: true, 
		projectName: 'EPBYMINW2629/MNTLAB-atsuranau-child1-build-job', 
		target: '.'])

	wrap([$class: 'TimestamperBuildWrapper']) {
     	echo "\u2600 Job was triggered and finished"
   }

stage '\u2780 Packing and Publishing'

	sh 'tar -xzf atsuranau_dsl_script.tar.gz'
	sh 'cp build/libs/mntlab-ci-pipeline.jar gradle-simple.jar'
	sh 'tar -zcf pipeline-atsuranau-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile gradle-simple.jar'
	

	wrap([$class: 'TimestamperBuildWrapper']) {
     	echo "\u2600 Job was triggered and finished"
   }

} // node

