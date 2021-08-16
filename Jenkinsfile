node("windows_label") {
	def environment 	= env.ENV_NAME.toLowerCase();
	def git_credentials_Id	= env.GITHUB_SSH_CRED_ID;
	def artifactory_url	= env.ARTIFACTORY_URL;
	def sonar_url	 	= env.SONAR_URL;
	def sonar_credential_Id	= env.SONAR_CREDENTIAL_ID;
	def artifactory_Server	= Artifactory.server env.ARTIFACTORY_CRED_ID;	
	
	def appGitUrl;
	def appGitBranch;
	def majorVersion;
	def minorVersion;
	def patchVersion;
	def devTeamEmail;
	
	def sonar_url;
	def sonarProjectKey;
	def sonarProjectSrc;
	def sonarBuildBreaker;
	def sonarProjectName;
	def sonarDoScan;

	def artifactPackageName;
	def artifactPackagePatten;
	def artifactName;
	
	def branchProperties 	= readProperties file: "${configGitRepoName}/${environment}/branch.properties"
	def pipelineProperties	= readProperties file: "${configGitRepoName}/${environment}/pipeline.properties"
	
	stage("Initialize") {
		cloneRepo("github.com/nugensol/devops-platform.git", "master");

		sonar_url = pipelineProperties.SONAR_PROJECT_NAME;
		sonarProjectKey = pipelineProperties.SONAR_PROJECT_KEY;
		sonarProjectSrc = pipelineProperties.SONAR_PROJECT_SOURCE;
		sonarBuildBreaker = pipelineProperties.SONAR_SKIP_BUILD_BREAKER;
		sonarProjectName = pipelineProperties.SONAR_PROJECT_NAME;
		sonarDoScan = pipelineProperties.SONAR_DO_SCAN;

		artifactPackageName = pipelineProperties.ARTIFACT_PACKAGE_NAME;
		artifactPackagePatten = pipelineProperties.ARTIFACT_PACKAGE_PATTERN;
		artifactName = pipelineProperties.ARTIFACT_NAME;
		
		appGitUrl = branchProperties.APP_GIT_URL;
		appGitBranch = branchProperties.APP_GIT_BRANCH;
		majorVersion = branchProperties.MAJOR_VERSION;
		minorVersion = branchProperties.MINOR_VERSION;
		patchVersion = branchProperties.PATCH_VERSION;
		devTeamEmail = branchProperties.DEV_TEAM_EMAILS;
	}
	
	stage("Checkout") {
		cloneRepo(appGitUrl, appGitBranch);
	}
	stage("Build Automation") {
		bat "c:\\jenkins_home\\apps\\maven-3.0\\bin\\mvn clean package"
	}
	stage("Unit Test") {
		bat "c:\\jenkins_home\\apps\\maven-3.0\\bin\\mvn test"
	}
	stage("Code Analysis") {
		bat "c:\\jenkins_home\\apps\\sonar-scanner-2.8\\bin\\sonar-scanner.exe -Dsonar.projectKey=${sonarProjectKey} -Dsonar.host.url=${sonarUrl} -Dsonar.projectName=${sonarProjectVersion} -Dsonar.sources=${sonarProjectSrc} -Dsonar.projectVersion=${sonarProjectVersion}"	
	}
	stage("Build Management") {
		archiveArtifacts artifacts: 'target/*.jar'
	}
	stage("Deployment") {

	}

}
def cloneRepo(gitRepo, branchName)
{
	try{
		withCredentials([string(credentialsId: 'git_credentials_token', variable: 'git_credentials_token')]) {
			bat "git clone -b ${branchName} https://${git_credentials_token}@${gitRepo}";
		}
	}catch(Exception e){
		println "Failed to clone the repo"
	}
}
