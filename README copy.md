For the BITBUCKET_HOME directory

    docker volume create --name bitbucketVolume

Start Atlassian Bitbucket Server:

    docker run \
        -v bitbucketVolume:/var/atlassian/application-data/bitbucket \
        --name="bitbucket" \
        -d \
        -p 7990:7990 \
        -p 7999:7999 \
        atlassian/bitbucket-server

Success.

    Bitbucket is now available on http://localhost:7990

For the PD db:

    docker volume create --name pgVolume

External DB:

    docker run \
        -v pgVolume:/var/lib/postgresql/data \
        --rm \
        -p 5432:5432 \
        --name postgres \
        -e POSTGRES_PASSWORD=123 \
        -e POSTGRES_USER=postgres \
        -e POSTGRES_DB=postgres \
        -d \
        postgres

psql:

    brew install postgres
    psql -h localhost -p 5432 -U postgres -W postgres
    postgres=#

psql prompt:

    CREATE ROLE bitbucketuser WITH LOGIN PASSWORD 'jellyfish' VALID UNTIL 'infinity';

    CREATE DATABASE bitbucket WITH ENCODING='UTF8' OWNER=bitbucketuser CONNECTION LIMIT=-1;

    db:     bitbucket
    login:  bitbucketuser
    pass:   jellyfish

PV PVC

    PV
      bitbucket
      postgres

    PVC
      bitbucket
      postgres

    path
      /var/atlassian/application-data/bitbucket
      /var/lib/postgresql/data

Deploy

    BBS
      Pod - 7990, 7999
    PG
      Pod - 5432

SVC

    BBS
      Deploy BBS - 7990, 7999
    PG
      Deploy PG - 5432

Exposing

    LoadBalancer
    NodePort

    Ingress + Cert-Manager

Firewall open port 7990

Debug using port-forward

    kubectl port-forward bitbucket-589885886-xm485 7990:7990
