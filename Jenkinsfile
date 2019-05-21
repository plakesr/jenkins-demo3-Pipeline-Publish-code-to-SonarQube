def label = "worker-${UUID.randomUUID().toString()}"

podTemplate(label: label, containers: [
  containerTemplate(name: 'maven', image: 'sureshchandrarhca15/myjenkins-slave:v2.0', command: 'cat', ttyEnabled: true, workingDir: '/tmp'),
])

{
  node(label) {
     def myRepo, gitCommit, gitBranch, shortGitCommit, previousGitCommit
     stage('Checkout') 
     {
        try {
           myRepo = checkout scm
           gitCommit = myRepo.GIT_COMMIT
           gitBranch = myRepo.GIT_BRANCH
           shortGitCommit = "${gitCommit[0..10]}"
           previousGitCommit = sh(script: "git rev-parse ${gitCommit}~", returnStdout: true)
        }
        catch(Exception e) {
           println "ERROR => maven build failed, exiting..."
           throw e
        }
     }
     stage('Build') {
      try {
        container('maven') {
            sh """
              pwd
              echo "GIT_BRANCH=${gitBranch}"
              echo "GIT_COMMIT=${gitCommit}"
              mvn -version
            """
        }
      }
      catch (Exception e) {
        println "Failed to test - ${currentBuild.fullDisplayName}"
        throw e
      }
    }
  }
}
