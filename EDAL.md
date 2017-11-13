# EDAL
EDAL can be used to set up a WMS service (Web map service).
The easiest way is using the [ncWMS](https://github.com/Reading-eScience-Centre/ncwms) application.

The docker file documented in the ncWMS documentation is not available on dockerhub, so we used [this dockerfile](https://hub.docker.com/r/pamtrak06/ncwms/).


## WMS API
The API provided by ncWMS includes the default WMS specification, and additional parameters that control the visuals, e.g. the colormap. The API specification can be found [here](https://reading-escience-centre.gitbooks.io/ncwms-user-guide/content/04-usage.html). 
