node('master') {
	def dirArtifactName = "nsDashboard"
	def artifactUrl = "github.com/Nasajon/${dirArtifactName}.git"
	def artifactId = "nsDash"
	def artifactIdCreateUser = "nsDashCreateUser"
	def artifactBuildPath = "${env.WORKSPACE}\\${dirArtifactName}\\"
	def nasajonCIBaseDir = "${env.NASAJON_CI_BASE_DIR}"
	def branchName = "${env.BRANCH_NAME}"
	
    properties([disableConcurrentBuilds()])


    stage('Checkout') {
        dir("${dirArtifactName}") {
                checkout scm
            timeout(time: 3600, unit: 'SECONDS') {
            }
        }
    }

    stage('Build') {
        dir("${nasajonCIBaseDir}\\build\\erp") {
            bat 'init.bat'
        }
        generateVersionNumber()
    }

    if(!branchName.startsWith("PR")){
        stage('Deploy') {
            dir("${nasajonCIBaseDir}\\build\\erp") {

                bat "sign_file.bat C:\\Users\\root\\Downloads\\nsDash.exe"
            }
        }
    }
}

def generateVersionNumber() {
	def version = ""
	def branchName = "${env.BRANCH_NAME}"

	if (branchName == "master") {
		version = "2.${env.CURRENT_SPRINT}.0.${env.BUILD_NUMBER}"
	} else if (branchName.startsWith("v2.")) {
		def sprint = branchName.substring(3)
		version = "2.${sprint}.${env.BUILD_NUMBER}.0"
	} else {
		version = "2.0.0.0"
	}

	println("Version: " + version)

	currentBuild.displayName = version

	def file_version_template = readFile("${env.WORKSPACE}\\nsDashboard\\version_info.txt")

	file_version_template = file_version_template
		.replaceAll("__VERSION_INFO1__", version.replace(".", ", "))
		.replaceAll("__VERSION_INFO2__", version)

	writeFile file: "${env.WORKSPACE}\\output\\VersionInfo", text: version
	writeFile file: "${env.WORKSPACE}\\output\\VersionInfo2", text: file_version_template
}
