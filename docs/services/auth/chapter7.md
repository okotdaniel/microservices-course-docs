
#### 1. Sign in to Docker Hub
To push images to Docker Hub, you need to sign in with your Docker account.

- Open a terminal and run:
    ```bash
    docker login
    ```
- Enter your Docker Hub username and password when prompted.

Alternatively, you can sign in via the Docker Desktop application.

#### 2. Create a Repository
Before pushing your image, you need a repository to store it.

- Go to [Docker Hub](https://hub.docker.com).
- Click on **Create Repository**.
- Fill in the following details:
    - **Repository Name**: Choose a name for your repository (e.g., `my-app`).
    - **Visibility**: Select **Public** or **Private** based on your preference.
- Click **Create** to finalize.


#### 3. Build Your Docker Image
Before tagging and pushing your image, you need to build it from your Dockerfile.

- Navigate to the directory containing your Dockerfile.
- Run the following command to build the image:
    ```bash
    docker build -t docker_username/app_name .
    ```
- Example:
    ```bash
    docker build -t my-app:1.0 .
    docker build -t <DOCKER_USERNAME>/getting-started-todo-app .
    ```

This command creates a Docker image with the specified tag (`local-image:tagname`) based on the instructions in your Dockerfile.

By default it will create an image with the latest tag.


#### 4. Tag Your Docker Image
Tag your local Docker image to match the repository name on Docker Hub.

- Use the following command:
    ```bash
    docker tag local-image:tagname your-dockerhub-username/repository-name:tagname
    docker build  -t okotdaniel/auth-service .
    ```
- Example:
    ```bash
    docker tag my-app:1.0 johndoe/my-app:1.0
    ```

#### 5. Push the Image to Docker Hub
Push the tagged image to your Docker Hub repository.

- Run the following command:
    ```bash
    docker push your-dockerhub-username/repository-name:tagname
    ```
- Example:
    ```bash
    docker push johndoe/my-app:1.0
    ```

#### 6. Verify the Image on Docker Hub
- Visit your Docker Hub account and navigate to the repository.
- Confirm that the image has been successfully uploaded.

### Recap
In this tutorial, you learned how to:

- Sign in to Docker Hub.
- Create a repository to store your image.
- Tag and push your Docker image to Docker Hub.

By following these steps, you can easily share your containerized applications with others or use them in your own projects.

### Next Steps
Explore Docker Hub to discover other images you can use as a base for your projects. Additionally, consider learning about private registries if you need to store sensitive or proprietary images.

