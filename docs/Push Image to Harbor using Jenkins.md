## Resolve Docker Permission Error

To allow the Jenkins user to run Docker commands without permission errors:

1. Add Jenkins user to the Docker group:

    ```bash
    sudo usermod -aG docker jenkins
    ```

2. Set access control list (ACL) for the Jenkins user on the directory:

    ```bash
    setfacl -m g:anon:rwx /home/anon/
    setfacl -m g:anon:rwx /home/anon/
    ```

3. Verify ACL settings:

    ```bash
    getfacl /home/anon/
    ```

    Output should look like:

    ```plaintext
    # file: .
    # owner: anon
    # group: anon
    user::rwx
    group::---
    group:anon:rwx
    mask::rwx
    other::---
    ```

## Login to Docker Registry

To log in to the Docker registry from Jenkins using a password stored in a file:

1. Store the password in a file (`my_password.txt`).
2. Use the following command to log in:

    ```bash
    cat my_password.txt | docker login mero.com --username admin --password-stdin
    ```

## Using the Jenkins Plugin for Docker

1. **Install Jenkins Docker Plugins:**
   - Docker
   - Docker Pipeline

2. **Configure Jenkins Job:**

    - **Repository Name:**
      Set the repository name to `project_name/image_name`. For example:

      ```plaintext
      minato/mero_zon
      ```

### Notes

- Ensure the Jenkins user has the necessary permissions to access the Docker socket.
- Replace `docker-credentials-id` with the actual ID of your Docker credentials in Jenkins.
- Ensure your Jenkins agent has Docker installed and configured correctly.
- Verify that your Docker registry (Harbor) is accessible from the Jenkins server.



![[Pasted Graphic 1.png]]

