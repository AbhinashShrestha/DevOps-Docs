Create a docker file 
```Dockerfile
FROM rockylinux:9.3

RUN yum  -y update

RUN yum -y install unzip

RUN mkdir /opt/tomcat/

WORKDIR /opt/tomcat

RUN curl -O https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.23/bin/apache-tomcat-10.1.23.tar.gz

RUN tar xvfz apache*.tar.gz

RUN mv apache-tomcat-10.1.23/* /opt/tomcat/.

RUN dnf -y install java

RUN java -version

  

WORKDIR /opt/tomcat/webapps

RUN curl -O https://www.free-css.com/assets/files/free-css-templates/download/page291/dozecafe.zip

RUN unzip dozecafe.zip -d dozecafe

RUN mv /opt/tomcat/webapps/dozecafe/html /opt/tomcat/webapps/dozecafe_website

ADD server.xml /opt/tomcat/conf/

EXPOSE 8080

  

CMD ["/opt/tomcat/bin/catalina.sh","run"]
```
Now in server.xml in your own local host and in specific path
```server.xml
  <!-- Service -->
  <Service name="Catalina">
    <Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               maxParameterCount="1000" />
    
    <!-- Engine -->
    <Engine name="Catalina" defaultHost="localhost">
      <!-- Host for your website -->
      <Host name="dozecafe-website" appBase="webapps"
            unpackWARs="true" autoDeploy="true">
        <Alias>dozecafe-website.com</Alias>
        <Context path="" docBase="/opt/tomcat/webapps/dozecafe_website" />
      </Host>
    </Engine>
  </Service>
</Server>
```
- Then 
	- Build the image
	```bash
	docker build -t name_of_image dir_in_which_Dockerfile
```
	- Then run (this is for mac so you might need to reconfigure some networking)
	```bash
	docker run -d -p 80:8080 --add-host host.docker.internal:host-gateway coffee_tomcat
```
