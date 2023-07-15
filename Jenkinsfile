def orgList = ['APIGEE_ORGANISATION_NAME']

// Parameters Separated with Separator
properties([
    parameters([
        [
          $class: 'ChoiceParameter',
          choiceType: 'PT_SINGLE_SELECT',
          description: 'Select the Organisation',
          name: 'ORGANISATION',
          script: [
              $class: 'GroovyScript',
              script: [classpath: [], sandbox: true, script: "return ${orgList.inspect()}"]
          ]
        ],
        // Separator
        separator(name: "ADD_APP", sectionHeader: "Add App",
          separatorStyle: "border-width: 0",
          sectionHeaderStyle: """
            background-color: #7ea6d3;
            text-align: left;
            padding: 4px;
            color: #343434;
            font-size: 22px;
            font-weight: normal;
            text-transform: uppercase;
            font-family: 'Orienta', sans-serif;
            letter-spacing: 1px;
            font-style: italic;
          """
        ),
        [
          $class: 'DynamicReferenceParameter', 
          choiceType: 'ET_FORMATTED_HTML', 
          description: 'Enter the App Name to be created ',
          name: 'NEW_APP_NAME', 
          omitValueField: true,
          referencedParameters: 'ENVIRONMENT',
          script: [
              $class: 'GroovyScript', 
              fallbackScript: [
                  classpath: [],
                  sandbox: true,
                  script: 
                      'return [\'Error message\']'
              ], 
              script: [
                  classpath: [], 
                  sandbox: true,
                  script: 
                      """ 
                          html=""
                          if (ENVIRONMENT.contains('')){
                              html="<input name='value' value='' class='setting-input' type='text'>"
                          }
                          else {
                            
                              html="Enter value in PRODUCT_NAME to enter the value"
                          }
                          return html
                      """
              ]
          ]
        ],
        [
          $class: 'DynamicReferenceParameter', 
          choiceType: 'ET_FORMATTED_HTML', 
          description: 'Mention the Product to associate with the App',
          name: 'ASSOCIATED_PRODUCT_NAME', 
          omitValueField: true,
          referencedParameters: 'ENVIRONMENT',
          script: [
              $class: 'GroovyScript', 
              fallbackScript: [
                  classpath: [],
                  sandbox: true,
                  script: 
                      'return [\'Error message\']'
              ], 
              script: [
                  classpath: [], 
                  sandbox: true,
                  script: 
                      """ 
                          html=""
                          if (ENVIRONMENT.contains('')){
                              html="<input name='value' value='' class='setting-input' type='text'>"
                          }
                          else {
                            
                              html="Enter value in PRODUCT_NAME to enter the value"
                          }
                          return html
                      """
              ]
          ]
        ],
        [
          $class: 'DynamicReferenceParameter', 
          choiceType: 'ET_FORMATTED_HTML', 
          description: 'Enter Developer Email for the App',
          name: 'DEVELOPER_EMAIL', 
          omitValueField: true,
          referencedParameters: 'ENVIRONMENT',
          script: [
              $class: 'GroovyScript', 
              fallbackScript: [
                  classpath: [],
                  sandbox: true,
                  script: 
                      'return [\'Error message\']'
              ], 
              script: [
                  classpath: [], 
                  sandbox: true,
                  script: 
                      """ 
                          html=""
                          if (ENVIRONMENT.contains('')){
                              html="<input name='value' value='' class='setting-input' type='text'>"
                          }
                          else {
                            
                              html="Enter value in PRODUCT_NAME to enter the value"
                          }
                          return html
                      """
              ]
          ]
        ]
    ])
])


pipeline {
    agent any
    environment {
          GCLOUD_DIR = "$JENKINS_HOME/google-cloud-sdk/bin"
          APIGEE_CLI_DIR = "$HOME/.apigeecli/bin"
    }
    stages {
        stage('Installing Dependencies') {
      steps {
          sh '''#!/bin/bash
                echo "Checking for pre-installed dependencies..."
                echo ""
                if [ ! -d "$GCLOUD_DIR" ]; then
                    echo "Installing GCloud CLI..."
                    echo ""
                    cd $JENKINS_HOME
                    curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-412.0.0-linux-x86_64.tar.gz
                    tar -xf google-cloud-cli-412.0.0-linux-*.tar.gz
                    ./google-cloud-sdk/install.sh -q
                    source $JENKINS_HOME/google-cloud-sdk/completion.bash.inc
                    source $JENKINS_HOME/google-cloud-sdk/path.bash.inc
                else--
                    echo "GCloud CLI is already Installed!"
                    echo ""
                fi

                if [ ! -d "$APIGEE_CLI_DIR" ]; then
                    echo "Installing Apigee CLI..."
                    echo ""
                    curl -L https://raw.githubusercontent.com/apigee/apigeecli/main/downloadLatest.sh | sh -
                    
                else
                    echo "Apigee CLI is already Installed!"
                    echo ""
                fi
             '''
      }
    }
        // Logging into GCloud
        stage('Logging into Google Cloud and Get Access Token') {
          steps {
            script {
                withCredentials([file(credentialsId: '<gcp_service_account>', variable: 'GOOGLE_SERVICE_ACCOUNT_KEY')]) {
                sh '${GCLOUD_DIR}/gcloud auth activate-service-account --key-file ${GOOGLE_SERVICE_ACCOUNT_KEY}'
                env.TOKEN = sh([script: "${GCLOUD_DIR}/gcloud auth print-access-token", returnStdout: true ]).trim()
              }
            }
          }
        }
        stage('Add apps') {
         steps {
          script {
          def APP_NAME = "${params.NEW_APP_NAME}"
          def curlOutput = sh(returnStdout: true, script: """curl --request POST \
            'https://apigee.googleapis.com/v1/organizations/${params.ORGANISATION}/developers/${params.DEVELOPER_EMAIL}/apps' \
            --header 'Authorization: Bearer ${env.TOKEN}' \
            --header 'Accept: application/json' \
            --header 'Content-Type: application/json' \
            --data '{"attributes":[{"name":"DisplayName","value":"${params.NEW_APP_NAME}"}],"apiProducts":["${params.ASSOCIATED_PRODUCT_NAME}"],"name":"${params.NEW_APP_NAME}","status":"approved"}' \
            --compressed""")
        
          }
        }
     }
  }
}
  
