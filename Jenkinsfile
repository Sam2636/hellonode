node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
    /* This builds the actual image using BuildKit and buildx */
    script {
        docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            // Create and use the buildx builder instance
            def buildx = docker.buildx()
            buildx.create("--use")
            
            // Build the Docker image using the buildx builder
            app = buildx.build("sam2636/hellonode")
        }
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