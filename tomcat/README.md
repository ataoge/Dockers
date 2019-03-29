#docker-tomcat8
Builds Tomcat8 on an Ubuntu server, to facilitate components in the ESRI ArcGIS Enterprise ecosystem, including Web Adaptor.

It's just a basic tomcat8 server though so no reason you can't use it for other things!

Note that it's running on ports 80/443 not 8080/8443! This is to better accomodate Web Adaptor.

#Build the Docker Image
docker build -t ataoge/tomcat8 .