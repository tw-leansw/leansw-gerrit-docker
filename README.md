# Gerrit Docker image
The Gerrit docker image integrated with PostgreSQL.

## Versions
> owenyang/gerrit:latest -> 2.11.4

## Container Quickstart
  1. Initialize and start gerrit.

    `$ docker run -d -p 8080:8080 -p 29418:29418 owenyang/gerrit`

  2. Open your browser to http://<docker host url>:8080

## Use another container as the gerrit site storage.
  1. Create a volume container.

    `docker run --name gerrit_volume owenyang/gerrit echo "Gerrit volume container."`

  2. Initialize and start gerrit using volume created above.

    `docker run -d --volumes-from gerrit_volume -p 8080:8080 -p 29418:29418 owenyang/gerrit`

## Use local directory as the gerrit site storage.
  1. Create a site directory for the gerrit site.

    `mkdir ~/gerrit_volume`

  2. Initialize and start gerrit using the local directory created above.

    `docker run -d -v ~/gerrit_volume:/var/gerrit/review_site -p 8080:8080 -p 29418:29418 owenyang/gerrit`

## Sync timezone with the host server. 
   `docker run -d -p 8080:8080 -p 29418:29418 -v /etc/localtime:/etc/localtime:ro owenyang/gerrit`

## Sample config
config for docker compose, integrated with PostgreSQL.
```yaml
pg-gerrit:
  image: postgres:9.4
  volumes:
    - ./data:/var/lib/postgresql/data
  ports:
    - "5432:5432"
  environment:
    - POSTGRES_USER=gerrit
    - POSTGRES_PASSWORD=gerritpass
    - POSTGRES_DB=gerritdb

gerrit:
  image: owenyang/gerrit
  volumes:
    - ./review_site:/var/gerrit/review_site
  ports:
    - "8080:8080"
    - "29418:29418"
  environment:
    - AUTH_TYPE=OpenID
    - WEBURL=< your host url >
    - DATABASE_TYPE=postgresql
  links:
    - pg-gerrit:db
```

### Miscellaneous
inspired from `openfrontier/docker-gerrit`