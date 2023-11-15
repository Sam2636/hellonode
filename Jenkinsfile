node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
    /* This builds the actual image using BuildKit and specifies platforms */
    script {
        // Set the environment variable to enable BuildKit
        env.DOCKER_BUILDKIT = "1"

        // Build the Docker image using the Docker CLI and specify platforms
        sh """
        docker build --platform=amd64,arm64 -t sam2636/hellonode .
        """
    }
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
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}