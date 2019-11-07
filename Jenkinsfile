#!/usr/bin/groovy

@Library(['github.com/indigo-dc/jenkins-pipeline-library@1.2.3']) _

pipeline {
    agent {
        label 'docker-build'
    }

    environment {
        dockerhub_repo = "deephdc/deep-oc-satsr"
        base_cpu_tag = "1.12.0-py3"
        base_gpu_tag = "1.12.0-py3"
    }

    stages {
        stage('Docker image building') {
	    /*
            when {
                anyOf {
                    branch 'master'
                    branch 'test'
                    buildingTag()
                }
            }
            */
            steps{
                checkout scm
                script {
                    // build different tags
                    id = "${env.dockerhub_repo}"

                    // GPU
                    image_id = DockerBuild(id,
                                           tag: ['test_run'],
                                           build_args: ["tag=${env.base_gpu_tag}",
                                                        "branch=master"])
                }
            }
            post {
                failure {
                    DockerClean()
                }
            }
        }

	stage('App validation') {
            steps {
                script {
                    echo "docker ID: $image_id"
                    image_id.each {
                        println "${it}"
                    	sh "docker run -d --rm -p 5000:5000 -p 6006:6006 -p 8888:8888 ${it}"
		    }
                }
            }
        }

	/*
        stage('Docker Hub delivery') {
            when {
                anyOf {
                    branch 'master'
                    branch 'test'
                    buildingTag()
                }
            }
            steps{
                script {
                    DockerPush(id_cpu)
                    DockerPush(id_gpu)
                }
            }
            post {
                failure {
                    DockerClean()
                }
                always {
                    cleanWs()
                }
            }
        }

        stage("Render metadata on the marketplace") {
            when {
                allOf {
                    branch 'master'
                    changeset 'metadata.json'
                }
            }
            steps {
                script {
                    def job_result = JenkinsBuildJob("Pipeline-as-code/deephdc.github.io/pelican")
                    job_result_url = job_result.absoluteUrl
                }
            }
        }
	*/
    }
}
