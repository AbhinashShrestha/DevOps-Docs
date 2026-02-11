### Step 1: Install Apache Tomcat 10 on Rocky Linux 9

1. **Install Java Development Kit (JDK)**:
   Ensure JDK is installed on your system. Tomcat requires Java to run.

2. **Download and Extract Apache Tomcat**:
   Download the latest version of Apache Tomcat from the official website, and extract it to your desired directory (`/opt` is a common choice).

3. **Configure Environment Variables (Optional)**:
   Set `CATALINA_HOME` and `JAVA_HOME` environment variables in your `.bashrc` or `.profile` file:

   ```bash
   export JAVA_HOME=/path/to/your/java
   export CATALINA_HOME=/path/to/your/tomcat
   ```

   Replace `/path/to/your/java` and `/path/to/your/tomcat` with the actual paths.

4. **Start Apache Tomcat**:
   Start Tomcat using the startup script:

   ```bash
   $CATALINA_HOME/bin/startup.sh
   ```

   Verify Tomcat is running by accessing `http://localhost:8080` in a web browser.

### Step 2: Manage Tomcat Applications with `curl`

Now, let's use `curl` commands to manage applications deployed on Tomcat via the Tomcat Manager application.

#### List Deployed Web Applications

To list all deployed web applications:

```bash
curl -v -u tomcat:tomcat http://localhost:8080/manager/text/list
```

Replace `tomcat:tomcat` with your Tomcat Manager username and password. This command will list the deployed web applications along with their status.

#### Start a Deployed Web Application

To start a deployed web application (replace `/zon` with your actual context path):

```bash
curl -v -u tomcat:tomcat http://localhost:8080/manager/text/start?path=/zon
```

This command starts the application located at `/zon`.

#### Stop a Deployed Web Application

To stop a deployed web application (replace `/zon` with your actual context path):

```bash
curl -v -u tomcat:tomcat http://localhost:8080/manager/text/stop?path=/zon
```

This command stops the application located at `/zon`.

### Notes:

- Ensure that the Tomcat Manager application (`manager` and `manager-script` roles) is properly configured in Tomcat's `tomcat-users.xml` file.
- Adjust the URLs (`localhost`, `8080`) as per your Tomcat configuration.
- Replace `tomcat` with your actual Tomcat Manager username and password in the `curl` commands.
- Always secure your Tomcat Manager access with strong credentials and limit access to trusted networks.
