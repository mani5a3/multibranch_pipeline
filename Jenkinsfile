pipeline {
 agent any
 options {
    buildDiscarder(logRotator(numToKeepStr: '3', artifactNumToKeepStr: '3'))
  }
  parameters {
 booleanParam(name: 'skipstage', defaultValue: 'false', description: 'skipping the stage')
 choice(name: 'branchname', choices: ['master', 'mani_dev', 'test_dev'], description: 'Please enter branch name')
 }
  stages {
   stage("checkout"){
    steps {
	script {
	currentBuild.displayName= "${params.branchname}_${BUILD_NUMBER}"
	git branch: "${params.branchname}", url: 'https://github.com/mani5a3/game-of-life.git'
	}
 	}
   }
   stage("build"){
    steps {
	 bat 'mvn clean install'
 	}
   }
   stage("Approval for Artifacts push"){
   steps {
    timeout(time:2, unit:'MINUTES')
    {
        input message: 'Approval for pushing artifacts',
		id: 'userInput',
		submitter: 'mani'
    } 
   }
   }
   stage("Push Artifacts to Jfrog"){
   when {
    expression {
	 params.skipstage  != true
	}
   }
    steps {
	 script {
	  def SERVER_ID = 'artifactory'
	  def server = Artifactory.server SERVER_ID
	  def uploadSpec = """{
			"files": [
				{
				  "pattern": "gameoflife-web/target/*.war",
				  "target": "example-repo-local"
				},
				{
				  "pattern": "gameoflife-core/target/*.jar",
				  "target": "example-repo-local"
				}			
			]
		}"""
	  server.upload(uploadSpec)
	 }
	}
   
   }

 }
    post{
        always{
            mail to: "manikantagaddam.g@outlook.com",
            subject: "Jenkins pipeline noitification",
            body: "${BUILD_URL}"
        }
    }
}