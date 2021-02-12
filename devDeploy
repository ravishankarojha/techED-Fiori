@Library('piper-library-os') _

node() {
  
  def check
  // def majorVersion="1.0.${BUILD_NUMBER}"
  def majorVersion="${BUILD_NUMBER}"
  currentBuild.displayName = majorVersion
  def GIT_COMMIT
  
  stage('prepare') {
      check=checkout scm
      echo "${check}"
      setupCommonPipelineEnvironment script:this
      checkChangeInDevelopment script: this,changeDocumentId:'8000004988'     
      GIT_COMMIT = check.GIT_COMMIT
      echo ">>>>>>>${GIT_COMMIT}"
       }

//stage('build') {
  //    mtaBuild script: this
  //   // step([$class: 'UploadBuild',tenantId: "5ade13625558f2c6688d15ce",revision: "${check.GIT_COMMIT}",appName: "SAP_Build",requestor: "admin",id: "${BUILD_NUMBER}"])
//}
echo "(*******)"
stage('Publish Artificats to UCD'){	
  step([$class: 'UCDeployPublisher',
	        siteName: 'UCD',
	        component: [
	            $class: 'com.urbancode.jenkins.plugins.ucdeploy.VersionHelper$VersionBlock',
	            componentName: 'ABAP',
	            createComponent: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.ComponentHelper$CreateComponentBlock',
	                componentTemplate: '',
	                componentApplication: 'SAP_ABAP-BE'
	            ],
	            delivery: [
	                $class: 'com.urbancode.jenkins.plugins.ucdeploy.DeliveryHelper$Push',
	                pushVersion: '2.0.${BUILD_NUMBER}',
	                //baseDir: '/var/jenkins_home/workspace/JPetStore/target',
			 baseDir: '/usar/ucd/temp/',
	                fileIncludePatterns: '*.*',
	                fileExcludePatterns: '',
	               // pushProperties: 'jenkins.server=Jenkins-app\njenkins.reviewed=false',
	                pushDescription: 'Pushed from Jenkins'
	            ]
	        ]
     ])
	  
          echo "(*******)"
	  echo "Demo1234 ${ABAP_VersionId}"
	  def newComponentVersionId = "${ABAP_VersionId}"
	  echo "git commit ${GIT_COMMIT}"

         step($class: 'UploadBuild', 
         tenantId: "5ade13625558f2c6688d15ce", 
         revision: "${GIT_COMMIT}", 
         appName: "SAP_ABAP-BE", 
         requestor: "admin", 
         id: "${newComponentVersionId}", 
         versionName: "2.0.${BUILD_NUMBER}"
      )
}
	stage ('Deploy to DEV') {
	step([$class: 'UCDeployPublisher',
		deploy: [ createSnapshot: [deployWithSnapshot: true, 
			 snapshotName: "2.0.${BUILD_NUMBER}"],
			 deployApp: 'SAP_ABAP-BE', 
			 deployDesc: 'Requested from Jenkins', 
			 deployEnv: 'DEV-ES1001', 
			 deployOnlyChanged: false, 
			 deployProc: 'deploy', 
			 deployReqProps: '', 
			 deployVersions: "ABAP:2.0.${BUILD_NUMBER}"], 
		siteName: 'UCD'])
 }
	
stage ('wait for deploy') {
	//sleep 10
	// echo 'Executing HCL One test ...'
	//sh '/var/jenkins_home/onetest/hcl-onetest-command.sh'
 }	
}
