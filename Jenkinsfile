node {
    stage('check'){
	    git branch: 'dsilnyagin', credentialsId: 'amazurenko4tests-passwd', url: 'https://github.com/MNT-Lab/mntlab-pipeline'
	    fileExists 'build.gradle'
	}
    stage('step1'){
	parallel (
            Branch1: {
          	stage ('Cucumber'){sh "/opt/gradle-4.0.1/bin/gradle cucumber"}
            },
            Branch2: {
          	stage ('jacocoTestReport'){sh "/opt/gradle-4.0.1/bin/gradle jacocoTestReport"}
            },
            Branch3: {
          	stage ('test'){sh "/opt/gradle-4.0.1/bin/gradle test"}
            }
        )
    }
}
