node{
  
    String jdktool = tool name: "Java_8", type: 'hudson.model.JDK'
   def nodeHome = tool name: 'node', type: 'nodejs'

    /* Set JAVA_HOME, and special PATH variables. */
    List javaEnv = [
       "PATH+MVN=${jdktool}/bin:${mvnHome}/bin",
       "N2_HOME=${nodeHome}",
       "JAVA_HOME=${jdktool}"
   ]
    
     withEnv(javaEnv) {
    stage ('Initialize') {
        sh '''
            echo "PATH = ${PATH}"
            echo "N2_HOME = ${N2_HOME}"
        '''
    }
    
    ws("${HOME}/agent/jobs/${JOB_NAME}/builds/${BUILD_ID}/") {               
            
           stage('Initialize') {
               sh '''
                    echo "PATH = ${PATH}"
                    echo "N2_HOME = ${N2_HOME}"
                '''
            }
        
        stage('Checkout') {
                
                    git(
                        url: 'https://github.com/xunrongl/DemoDRA-1.git',
                        branch: "master"
                    )
                
            }


            withCredentials([usernamePassword(credentialsId: '82ff0727-9418-4962-b96a-cb031dd19fc1', 
                passwordVariable: 'IBM_CLOUD_DEVOPS_CREDS_PSW', usernameVariable: 'IBM_CLOUD_DEVOPS_CREDS_USR')]) {
                def gitCommit = sh(returnStdout: true, script: "git rev-parse HEAD").trim()
                stage('Build') {
                   withEnv(["GIT_COMMIT=${gitCommit}",
                         'GIT_BRANCH=master',
                         "GIT_REPO=https://github.com/xunrongl/DemoDRA-1"]) {
                    try {
                         sh 'npm install'
                         sh 'grunt dev-setup --no-color' 
                           // junit 'target/surefire-reports/**/*.xml'
                        // use "publishBuildRecord" method to publish build record
//publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"SUCCESS", duration: 1, hostName: "local-dash.gravitant.net", serviceName: "Serve"
publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"SUCCESS", hostName: "local-dash.gravitant.net", serviceName: "Serve"
  
                    }
                    catch (Exception e) {
//publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"FAIL", duration : 11, hostName: "local-dash.gravitant.net", serviceName: "Serve"
publishBuildRecord gitBranch: "${GIT_BRANCH}", gitCommit: "${GIT_COMMIT}", gitRepo: "${GIT_REPO}", result:"FAIL", hostName: "local-dash.gravitant.net", serviceName: "Serve"
  
                    }
                    
                    
            }
                    
            }
             
}
        withCredentials([usernamePassword(credentialsId: 'f6c4700a-89d1-4532-936f-d8e3fd2d0600', 
                passwordVariable: 'IBM_CLOUD_DEVOPS_CREDS_PSW', usernameVariable: 'IBM_CLOUD_DEVOPS_CREDS_USR')]) {

                    stage('Unit Test and Code Coverage') {
                  
                        sh 'grunt dev-test-cov --no-color -f'
                    // use "publishTestResult" method to publish test result
//publishTestResult type:'unit', fileLocation: '/var/jenkins_home/workspace/Jenkins-Github/simpleTest.json'
                    publishTestResult type:'unit', fileLocation: './mochatest.json', serviceName: "Serve", hostName: "local-dash.gravitant.net", resultType: "junit"
                } 
                }
    }
}

