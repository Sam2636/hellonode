node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
    /* This builds the actual image using BuildKit and experimental features */
    script {
        // Set the environment variable to enable BuildKit
        env.DOCKER_CLI_EXPERIMENTAL = "enabled"

        // Build the Docker image using the Docker CLI and experimental features
        sh """
        docker build --platform linux/amd64,linux/arm64 -t sam2636/hellonode .
        docker manifest create sam2636/hellonode:latest --amend sam2636/hellonode:amd64 --amend sam2636/hellonode:arm64
        docker manifest push sam2636/hellonode:latest
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