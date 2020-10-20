# securityScanDockerimages

Security scan of docker files can be done via Claire.
https://techbeacon.com/security/10-top-open-source-tools-docker-security
https://github.com/arminc/clair-scanner/blob/master/README.md

Steps to run Claire from your docker
---------------------------------------------------------------------
docker run -d --name db arminc/clair-db:latest
docker run -d --link db:postgres --name clair arminc/clair-local-scan:v2.0.6
docker run --rm  -v /var/run/docker.sock:/var/run/docker.sock --network=container:clair ovotech/clair-scanner clair-scanner alpine

Don't forget to run docker build which you would like to scan

[docker build - < kafka.dockerfile]

Troubleshooting
---------------------------------------------------------------------

If you get [CRIT] ▶ Could not save Docker image [image:version]: Error response from daemon: reference does not exist, this means that image image:version is not locally present. You should have this image present locally before trying to analyze it (e.g.: docker pull image:version).

Errors like [CRIT] ▶ Could not analyze layer: Clair responded with a failure: Got response 400 with message {"Error":{"Message":"could not find layer"}} indicates that Clair can not retrieve a layer from clair-scanner. This means that you probably specified a wrong IP address in options (--ip). Note that you should use a publicly accessible IP when clair is running in a container, or it wont be able to connect to clair-scanner. If clair is running inside the docker, use the docker0 ip address. You can find the docker0 ip address by running ifconfig docker0 | grep inet

[CRIT] ▶ Could not read Docker image layers: manifest.json is not valid fires when image version is not specified and is required. Try to add :version (.e.g. :latest) after the image name.

[CRIT] ▶ Could not analyze layer: POST to Clair failed Post http://docker:6060/v1/layers: dial tcp: lookup docker on 127.0.0.53:53: no such host indicates that clair server could ne be reached. Double check hostname and port in -c argument, and your clair settings (in clair's docker-compose.yml for instance if you run it this way).
