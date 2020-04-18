node ('master') {    
      stage('Creating Jobs') {
        build job: 'seed'
      }
    
      stage('Preparation (Checking out)') {
        echo ("-> Preparation stage started.")
        git branch: 'meremin', url: ' https://github.com/rimus93/mntlab-pipeline'
        echo ("-> Preparation stage finished.")
    }
    
    stage('Building code') {
        echo ("-> Building code stage started.")
        sh ("/usr/local/share/applications/gradle-6.2.2/bin/gradle build")
        echo ("-> Building code stage finished.")
    }

    stage('Testing code') {
        echo ("-> Testing code stage started.")
        parallel cucumber: {
            sh ("/usr/local/share/applications/gradle-6.2.2/bin/gradle cucumber")
        },
        jacoco: {
            sh ("/usr/local/share/applications/gradle-6.2.2/bin/gradle jacocoTestReport")
        },
        gradle: {
            sh ("/usr/local/share/applications/gradle-6.2.2/bin/gradle test")
        }
        echo ("-> Testing code stage finished.")
    }

    stage('Triggering job and fetching artefact after finishing') {
        echo ("-> Triggering job and fetching artefact after finishing stage started.")
        build job: 'MNTLAB-meremin-child1-build-job',
        parameters: [string(name: 'BRANCH_NAME', value: 'meremin')]
        step ([$class: 'CopyArtifact',
                  projectName: 'MNTLAB-meremin-child1-build-job',
                  filter: 'meremin_dsl_script.tar.gz']);
        echo ("-> Triggering job and fetching artefact stage finished.")
    }

    stage('Packaging and Publishing results') {
        echo ("-> Packaging and Publishing results stage started.")
        sh ("tar -xzf meremin_dsl_script.tar.gz && cp build/libs/*.jar ./")
        sh ("tar -czvf pipeline-meremin-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile gradle-simple.jar")
        archiveArtifacts artifacts: 'pipeline-meremin-${BUILD_NUMBER}.tar.gz', onlyIfSuccessful: true
        echo ("-> Packaging and Publishing results stage finished.")
    }

    stage('Asking for manual approval') {
        echo ("-> Asking for manual approval stage started.")
        input message: 'Awaiting manual approval', ok: 'APPROVE'
        echo ("-> Asking for manual approval stage finished.")
    }

    stage('Deployment') {
        echo ("-> Deployment stage started.")
        sh ("java -jar gradle-simple.jar")
        echo ("-> Deployment stage finished.")
    }

    stage('Sending status') {
            echo ('SUCCESS')
    }
}

