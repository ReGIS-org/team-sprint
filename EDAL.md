# EDAL
EDAL can be used to set up a WMS service (Web map service).
The easiest way is using the [ncWMS](https://github.com/Reading-eScience-Centre/ncwms) application.

```
curl -L -O https://github.com/Reading-eScience-Centre/ncwms/releases/download/ncwms-2.2.11/ncWMS2-standalone.jar
java -jar ncWMS2-standalone.jar
# open in web browser: localhost:8080
# click on: Admin interface (requires login)
# add under Datasets / Required data / Location: path and name of data file (.nc)
# add under Datasets / Required data / ID: random string
# click on Save configuration
# Reload page until State: LOADING changes to State: READY
# results can be found under: ncWMS Frontpage
```

The docker file documented in the ncWMS documentation is not available on dockerhub, so we used [this dockerfile](https://hub.docker.com/r/pamtrak06/ncwms/).


## WMS API
The API provided by ncWMS includes the default WMS specification, and additional parameters that control the visuals, e.g. the colormap. The API specification can be found [here](https://reading-escience-centre.gitbooks.io/ncwms-user-guide/content/04-usage.html). 

## DataCatalogue
ncWMS provides an admin interface where you can add data. The admin page talks to the [NcwmsCatalogue](https://github.com/Reading-eScience-Centre/ncwms/blob/master/src/main/java/uk/ac/rdg/resc/edal/ncwms/NcwmsCatalogue.java). This is an implementation of the [WMSCatalogue](https://github.com/Reading-eScience-Centre/edal-java/blob/master/wms/src/main/java/uk/ac/rdg/resc/edal/wms/WmsCatalogue.java) and an extension of the [DataCatalogue](https://github.com/Reading-eScience-Centre/edal-java/blob/master/xml-catalogue/src/main/java/uk/ac/rdg/resc/edal/catalogue/DataCatalogue.java).

An example of how the catalogue is being used to update the config, including adding files, can be found in the [NcwmsAdminServlet class](https://github.com/Reading-eScience-Centre/ncwms/blob/master/src/main/java/uk/ac/rdg/resc/edal/ncwms/NcwmsAdminServlet.java#L254). 
