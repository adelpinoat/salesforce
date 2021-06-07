#!groovy
import groovy.json.JsonSlurperClassic
node {

    def BUILD_NUMBER=env.BUILD_NUMBER
    def RUN_ARTIFACT_DIR="tests/${BUILD_NUMBER}"
    def SFDC_USERNAME

    def HUB_ORG="adelpino-2wj0@force.com"
    def SFDC_HOST = "https://login.salesforce.com"
    def JWT_KEY_CRED_ID = "02798db6-7f26-4419-801d-d870ad14bd66"
    def CONNECTED_APP_CONSUMER_KEY="3MVG9SOw8KERNN0.QsttwZsnrVXASD5DA.9xBIl2SHUWOu2CyJGmmtwQYvzcgzLYDLilrXV7PosqE56fktGdD"
    println 'KEY IS' 
    println JWT_KEY_CRED_ID
    def toolbelt = tool 'toolbelt'



    
    stage('checkout source') {
        // when running in multi-branch job, one must issue this command
        checkout scm
      }

    withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {
        stage('Create Scratch Org') {
            if (isUnix()) {
                rc = sh returnStatus: true, script: "${toolbelt} force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }else{
                 rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${HUB_ORG} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SFDC_HOST}"
            }
            if (rc != 0) { 
              error 'hub org authorization failed' 
              jiraNewIssue site: 'prueba1'
              }

            // need to pull out assigned username
              if (isUnix()) {
                echo "Init result: ${currentBuild.result}"
                echo "Init result: ${currentBuild.currentResult}"
                //rmsg = sh returnStdout: true, script: "${toolbelt} force:org:create --definitionfile config/enterprise-scratch-def.json --json --setdefaultusername"
              }else{
                   rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:org:create --definitionfile config/project-scratch-def.json --json --setdefaultusername"
              }
              if (rc != 0) { 
                error 'ERRROOOOOOOOOORRRRRRRRRRRR failed' 
                jiraNewIssue site: 'prueba1'
              }

            printf rmsg
            println('Hello from a Job DSL script!')
            println(rmsg)
            def beginIndex = rmsg.indexOf('{')
            def endIndex = rmsg.indexOf('}')
            println(beginIndex)
            println(endIndex)
            def jsobSubstring = rmsg.substring(beginIndex)
            println(jsobSubstring)
            
            def jsonSlurper = new JsonSlurperClassic()
            def robj = jsonSlurper.parseText(jsobSubstring)
            //if (robj.status != "ok") { error 'org creation failed: ' + robj.message }
            SFDC_USERNAME=robj.result.username
            robj = null
            
        }
        stage("currentresult"){
          when { 
                return (${currentBuild.currentResult} ==~ /(?i)(Y|FAILURE|SUCCES)/)
             }
          steps{
              echo "Este paso se ejecuta siempre"
              echo "Init result: ${currentBuild.result}"
              echo "Init result: ${currentBuild.currentResult}"
              echo "Init result: ${currentBuild.currentResult}"
              echo "Init result: ${currentBuild.currentResult}"
              echo "Init result: ${currentBuild.currentResult}"
              echo "Init result: ${currentBuild.currentResult}"
              echo "Init result: ${currentBuild.currentResult}"
              }
 
          }

          stage('Push To Test Org') {
              if (isUnix()) {
                    rc = sh returnStatus: true, script: "\"${toolbelt}\" force:source:push --targetusername ${SFDC_USERNAME}"
              }else{
                  rc = bat returnStatus: true, script: "\"${toolbelt}\" force:source:push --targetusername ${SFDC_USERNAME}"
              }
            if (rc != 0) {
                error 'push failed'
            }
            
          }

             
    }
}
