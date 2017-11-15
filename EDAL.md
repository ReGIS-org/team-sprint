# EDAL
EDAL can be used to set up a WMS service (Web map service).
The easiest way is using the [ncWMS](https://github.com/Reading-eScience-Centre/ncwms) application. 
This can be done in different ways.

## Starting ncWMS standalone with a jar file 

```bash
$ curl -L -O https://github.com/Reading-eScience-Centre/ncwms/releases/download/ncwms-2.2.11/ncWMS2-standalone.jar
$ java -jar ncWMS2-standalone.jar
# 1. open in web browser: localhost:8080
# 2. click on: Admin interface (admin/admin)
# 3. add under Datasets / Required data / Location: path and name of data file (.nc)
# 4. add under Datasets / Required data / ID: random string
# 5. click on Save configuration
# 6. Reload page until State: LOADING changes to State: READY
# 7. results can be found under: ncWMS Frontpage
```

When we ran ncWMS in the standalone version, the Godiva interface did not work.

## Starting ncWMS with docker

```bash
$ docker run -d -p 80:8080 -p 443:8443 guygriffiths/ncwms
# 1. open in a web browser: localhost/ncWMS
# 2. to add new data: click on Admin interface (ncwms/ncwns)
# and proceed as mentioned in steps 3-7 above
```

Or with another [dockerfile](https://github.com/axiom-data-science/docker-ncwms):

`docker pull axiom/docker-ncwms`

```bash
$ docker run \
    -d \
    -p 80:8080 \
    -p 443:8443 \
    -v /path/to/configdir:/usr/local/tomcat/.ncWMS2
    axiom/docker-ncwms
 ```
 
Note that saved data stored on a docker container may disappear after the container is stopped.

## Starting ncWMS with tomcat and a war file

```bash
$ cd /var/lib/tomcat/webapps
$ sudo curl -L -O https://github.com/Reading-eScience-Centre/ncwms/releases/download/ncwms-2.2.11/ncWMS2.war
# 1. add the following two lines to /etc/tomcat/tomcat-users.xml above </tomcat-users>
<role rolename="ncWMS-admin" />
<user username="admin" password="ncWMS-password" roles="ncWMS-admin"/>
$ sudo service tomcat start
# 2. open in w webbrowser: http://localhost:8080/ncWMS2/
# 3. to add new data: click on Admin interface (admin/ncWMS-password)
# and proceed as mentioned in steps 3-7 in the jar file instructions above
```
## Alongside WebDAV docker
The paths to data can also be external URLs. For example, to host and read from a webdav server:

```
docker run  -d -e USERNAME=webdav -e PASSWORD=webdav -v /path/to/data/:/var/webdav -p 8888:80 morrisjobke/webdav
```

Then the URL to a file on the web server is `http://webdav:webdav@172.17.42.1:8888/webdav/data.nc`. (The ip adress `172.17.42.1` is the adress used for the host inside the docker container).

## WMS API
The API provided by ncWMS includes the default WMS specification, and additional parameters that control the visuals, e.g. the colormap. The API specification can be found [here](https://reading-escience-centre.gitbooks.io/ncwms-user-guide/content/04-usage.html). 

## DataCatalogue
ncWMS provides an admin interface where you can add data. The admin page talks to the [NcwmsCatalogue](https://github.com/Reading-eScience-Centre/ncwms/blob/master/src/main/java/uk/ac/rdg/resc/edal/ncwms/NcwmsCatalogue.java). This is an implementation of the [WMSCatalogue](https://github.com/Reading-eScience-Centre/edal-java/blob/master/wms/src/main/java/uk/ac/rdg/resc/edal/wms/WmsCatalogue.java) and an extension of the [DataCatalogue](https://github.com/Reading-eScience-Centre/edal-java/blob/master/xml-catalogue/src/main/java/uk/ac/rdg/resc/edal/catalogue/DataCatalogue.java).

An example of how the catalogue is being used to update the config, including adding files, can be found in the [NcwmsAdminServlet class](https://github.com/Reading-eScience-Centre/ncwms/blob/master/src/main/java/uk/ac/rdg/resc/edal/ncwms/NcwmsAdminServlet.java#L254). 

Unfortunately, this NcwmsAdminServlet handles all admin stuff in one call. This means that we cannot request it to add a new file, without sending a complete form that also contains e.g. the existing datasets. We probably have to modify the code in NcwmsAdminServlet.

We have the following paramters for the services from the admin servlet:
```
addData
	dataset.new{i}.id
	dataset.new{i}.title
	dataset.new{i}.location
	dataset.new{i}.reader
	dataset.new{i}.disabled
	dataset.new{i}.queryable
	dataset.new{i}.updateinterval
	dataset.new{i}.moreinfo
	dataset.new{i}.copyright
	
updateData
	dataset.{id}.id
	dataset.{id}.title
	dataset.{id}.location
	dataset.{id}.reader
	dataset.{id}.disabled
	dataset.{id}.queryable
	dataset.{id}.downloadable
	dataset.{id}.updateinterval
	dataset.{id}.moreinfo
	dataset.{id}.copyright
	dataset.{id}.metadataUrl
	dataset.{id}.metadataDesc
	dataset.{id}.metadataMimetype
	dataset.{id}.refresh
	
removeData
	dataset.{id}.remove
	
addDynamicService
	dynamicService.new{i}.alias
	dynamicService.new{i}.servicePath
	dynamicService.new{i}.datasetIdMatch
	dynamicService.new{i}.moreinfo
	dynamicService.new{i}.copyright
	dynamicService.new{i}.disabled
	dynamicService.new{i}.queryable
	dynamicService.new{i}.reader
	
updateDynamicService
	dynamicService.{alias}.alias
	dynamicService.{alias}.servicePath
	dynamicService.{alias}.datasetIdMatch
	dynamicService.{alias}.moreinfo
	dynamicService.{alias}.copyright
	dynamicService.{alias}.disabled
	dynamicService.{alias}.queryable
	dynamicService.{alias}.reader

removeDynamicService
	dynamicService.{alias}.remove
	
configServer
	server.title
	server.abstract
	server.keywords
	server.url
	server.maximagewidth
	server.maximageheight
	server.allowfeatureinfo
	server.allowglobalcapabilities
	
configContact
	contact.name
	contact.org
	contact.tel
	contact.email
	
configCache
	cache.enable
	cache.inMemorySizeMB
	cache.elementLifetimeMinutes
```
