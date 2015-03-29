# docker-jenkins

A [Docker](https://docker.com/) container for [Jenkins](http://jenkins-ci.org/) with [Nginx](http://nginx.org/) in front of it providing HTTPS.

## Run the container

Using the `docker` command:

    CONTAINER="jenkinsdata" && sudo docker run \
      --name "${CONTAINER}" \
      -h "${CONTAINER}" \
      -v /jenkins/data \
      viljaste/data:latest

    CONTAINER="jenkins" && sudo docker run \
      --name "${CONTAINER}" \
      -h "${CONTAINER}" \
      -p 80:80 \
      -p 443:443 \
      --volumes-from jenkinsdata \
      -d \
      viljaste/jenkins:latest

Using the `fig` command

    TMP="$(mktemp -d)" \
      && git clone http://git.simpledrupalcloud.com/simpledrupalcloud/docker-jenkins.git "${TMP}" \
      && cd "${TMP}" \
      && sudo fig up

## Build the image

    TMP="$(mktemp -d)" \
      && git clone http://git.simpledrupalcloud.com/simpledrupalcloud/docker-jenkins.git "${TMP}" \
      && cd "${TMP}" \
      && sudo docker build -t viljaste/jenkins:latest . \
      && cd -

## Start the container automatically

    SERVER_NAME="localhost"
    
    TMP="$(mktemp -d)" \
      && git clone http://git.simpledrupalcloud.com/simpledrupalcloud/docker-jenkins.git "${TMP}" \
      && cd "${TMP}" \
      && sudo cp ./fig.yml /opt/jenkins.yml \
      && sudo sed -i "s/localhost/${SERVER_NAME}/g" /opt/jenkins.yml \
      && sudo cp ./jenkins.conf /etc/init/jenkins.conf \
      && cd -

## Back up Jenkins data

Back up a single Jenkins data container

    sudo docker run \
      --rm \
      --volumes-from jenkinsdata \
      -v $(pwd):/backup \
      viljaste/base:latest tar czvf /backup/jenkinsdata.tar.gz /jenkins

Back up all Jenkins data containers running on your host

    sudo tools/jenkinsdata backup
    
## Restore Jenkins data from a backup

    CONTAINER="jenkinsdata" && sudo docker run \
      --name "${CONTAINER}" \
      -h "${CONTAINER}" \
      -v /jenkins \
      viljaste/data:latest

    sudo docker run \
      --rm \
      --volumes-from jenkinsdata \
      -v $(pwd):/backup \
      viljaste/base:latest tar xzvf /backup/jenkinsdata.tar.gz

Restore all Jenkins data containers from a backup

    sudo tools/jenkinsdata restore

## Known issues

* https://issues.jenkins-ci.org/browse/JENKINS-9774

## License

**MIT**
