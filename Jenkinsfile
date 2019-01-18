pipeline {



    agent {
        label 'swarm'
    }

    // using the Timestamper plugin we can add timestamps to the console log
    options {
        timestamps()
    }

    tools{
        maven 'M3'
    }



    stages {
        stage('Preparation') {
            steps {
                git branch: 'master',changelog: false, credentialsId: 'promuks-github-connect', poll: false, url: 'https://github.com/promuks/java-maven-junit-helloworld'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -Dmaven.test.failure.ignore=true clean install'


            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    // we only worry about archiving the jar file if the build steps are successful
                    archiveArtifacts(artifacts: '**/target/*.jar', allowEmptyArchive: true)

                    script {
                        currentBuild.result = "SUCCESS"	// sets the ordinal as 0 and boolean to true
                        //currentBuild.build_agent_name = env.NODE_NAME
                        println "ENV BRANCH_NAME"+env.BRANCH_NAME
                        println "PROJECT TYPE"+(env.BRANCH_NAME.split("/"))[0]
                        echo currentBuild.displayName
                        echo currentBuild.fullDisplayName
                        //currentBuild.description = env.NODE_NAME
                        //currentBuild.displayName = env.NODE_NAME
                        //(()currentBuild.getRawBuild().getExecutor().getOwner().getNode()).setNodeName(env.NODE_NAME)
                        //echo currentBuild.fullDisplayName
                        String[] executorNameParts= env.NODE_NAME.split("-")
                        println(executorNameParts[1])
                        def pipelineDataMap = [:]
                        pipelineDataMap['executor_name'] = (env.NODE_NAME.split("-"))[1]
                        step([$class: 'InfluxDbPublisher',  customPrefix: env.JOB_NAME, customData: pipelineDataMap, customDataTags: pipelineDataMap, customProjectName: 'agentInfoSend', target: 'jenkins-data', measurementName: 'ExecutorInfo'])
                    }
                    println "***************************************"
                    sh 'printenv'
                    println "***************************************"


                    //step([$class: 'InfluxDbPublisher', target: myTarget, customPrefix: 'myPrefix', customData: myDataMap])

                    // step([$class: 'InfluxDbPublisher',  customPrefix: env.JOB_NAME, customData: pipelineDataMap, customProjectName: 'testInfluxDataSend', target: 'jenkins-data', measurementName: 'TestingZone'])
                }
            }
        }


    }

/*  post {
    failure {
      // notify users when the Pipeline fails
      mail to: 'team@example.com',
          subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
          body: "Something is wrong with ${env.BUILD_URL}"
    }
  }*/
}