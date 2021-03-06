/*
properties(
  [
    pipelineTriggers([cron('*/5 * * * *')]),
  ]
)
*/

node {
  def app
  def owner = "Douglas Meldrum"

  stage('Start') {
    emailext (
      subject: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
      recipientProviders: [[$class: 'DevelopersRecipientProvider']]
    )
  }
  stage('Clone repository') {
  /* Let's make sure we have the repository cloned to our workspace */

    checkout scm
  }

  stage('Pre flight') {
  /* This is were all the pre-build actions occur */

    echo "This project was hoodied by ${owner}"
  }

  stage('Example') {
    echo "Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
  }

  stage('Build image') {
  /* This builds the actual image; synonymous to
   * docker build on the command line */

    app = docker.build("getintodevops/hellonode")
  }

  stage('Test image') {
  /* Ideally, we would run a test framework against our image.
   * For this example, we're using a Volkswagen-type approach ;-) */

    app.inside {
      sh 'echo "Tests passed"'
    }
  }

  stage('Push image') {
  /* Finally, we'll push the image with two tags:
   * First, the incremental build number from Jenkins
   * Second, the 'latest' tag.
   * Pushing multiple tags is cheap, as all the layers are reused. */

    docker.withRegistry('https://stag-docker-repo.clix.io:5000') {
      app.push("${env.BUILD_NUMBER}")
      app.push("latest")
    }

    emailext (
      subject: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
      recipientProviders: [[$class: 'DevelopersRecipientProvider']]
    )
  }
}
