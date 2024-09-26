Setting up ActiveMQ involves several steps, from installation to configuring access and managing messages. Here’s a detailed guide based on your requirements:

### Installation and Setup

1. **Install Java Development Kit (JDK)**:
   Ensure Java 17 JDK and JDK development packages are installed:
   ```bash
   sudo dnf install java-17-openjdk java-17-openjdk-devel
   ```

2. **Download and Extract ActiveMQ**:
   Download the ActiveMQ binary distribution and extract it to `/opt`:
   ```bash
   sudo wget https://downloads.apache.org/activemq/6.1.2/apache-activemq-6.1.2-bin.tar.gz
   sudo tar zxvf apache-activemq-6.1.2-bin.tar.gz -C /opt
   ```

3. **Create a Dedicated User**:
   Create a system user specifically for ActiveMQ:
   ```bash
   sudo useradd activemq
   sudo chown -R activemq:activemq /opt/apache-activemq-6.1.2/
   ```

4. **Configure systemd Service**:
   Create a systemd service file for ActiveMQ:
   ```bash
   sudo vim /etc/systemd/system/activemq.service
   ```
   Paste the following configuration:
   ```
   [Unit]
   Description=Apache ActiveMQ Message Broker
   After=network-online.target
   
   [Service]
   Type=forking
   User=activemq
   Group=activemq
   WorkingDirectory=/opt/apache-activemq-6.1.2/bin
   ExecStart=/opt/apache-activemq-6.1.2/bin/activemq start
   ExecStop=/opt/apache-activemq-6.1.2/bin/activemq stop
   Restart=Never
   
   [Install]
   WantedBy=multi-user.target
   ```


5. **Modify Jetty Configuration for Remote Access**:
   Edit the `jetty.xml` configuration file to allow access from other devices:
   ```bash
   sudo vim /opt/apache-activemq-6.1.2/conf/jetty.xml
   ```
   Find the line `<property name="host" value="127.0.0.1"/>` and change it to:
   ```xml
   <property name="host" value="0.0.0.0"/>
   ```

6. **Restart ActiveMQ**:
   Restart the ActiveMQ service to apply the changes:
   ```bash
   sudo systemctl daemon-reload
   sudo systemctl start activemq
   sudo systemctl enable activemq
   ```

### Managing Messages with ActiveMQ

ActiveMQ provides command-line tools (`producer`, `consumer`, `browse`) for managing messages:

- **Producer**: Send messages to a specified queue.
  ```bash
  /opt/apache-activemq-6.1.2/bin/activemq producer --destination queue://myQueue
  ```

- **Consumer**: Consume messages from a specified queue.
  ```bash
  /opt/apache-activemq-6.1.2/bin/activemq consumer --destination queue://myQueue
  ```

- **Browse**: View messages within a queue.
  ```bash
  /opt/apache-activemq-6.1.2/bin/activemq browse myQueue
  ```

These commands help in efficiently managing and monitoring messages within your ActiveMQ environment, ensuring smooth communication between applications.

By following these steps, you can effectively set up, configure, and use ActiveMQ to handle messaging in your environment.