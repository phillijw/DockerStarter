# Docker Starter
Docker starter project for ASP.NET Core app with a JS SPA frontend served by nginx and a PostgreSQL database. 

# Quickstart
Install docker then run this on the host machine:

(don't forget to update `BACKEND-REPO-URL` & `FRONTEND-REPO-URL`)
```
apt-get -y update && apt-get -y install git \
&& git clone https://github.com/Biarity/DockerStarter.git /app/source \
&& backend-repo="BACKEND-REPO-URL" \ 
&& frontend-repo="FRONTEND-REPO-URL" \
&& cd /app/source && docker-compose build && docker-compose up -d
```
# Usage

* [OPTIONAL] Update `backend/Dockerfile` to build/run backend correctly (& wait for db connection)
    * currently set up to build & run .NET Core projects (`dotnet build` + `dotnet run`)

* [OPTIONAL] Update `frontend/Dockerfile` to build frontend source solution into `/app/dist` correctly
    * currently set up to build `npm run build` to `/dist`

* [OPTIONAL] Ensure DB connection string is set up in your backend source correctly
    * eg. `User Id=postgres; Database=postgres; Password=supersecretpassword; Host=db; Port=5432;`
    * database can't be accessed externally so no need to change password

1. Setup on host machine (ie. DigitalOcean machine)
    * SSH into host machine 
    * install docker
    * move this entire folder to host machine into `/app/source`
        * Using git `apt-get -y update && apt-get -y install git && git clone https://github.com/Biarity/DockerStarter.git /app/source`

2. Update repositories to pull form (via env vars)
    * these are the repos that will be cloned when you rebuild the images as below
    * `backend-repo="BACKEND-REPO-URL" ; frontend-repo="FRONTEND-REPO-URL"`
    
3. Start compose server (ie. to update all)
    * `cd /app/source && docker-compose build && docker-compose up -d`
    
* To only update backend 
    * `cd /app/source && docker-compose build backend && docker-compose up --no-deps -d backend`
    
* To only update frontend
    * `cd /app/source && docker-compose build frontend && docker-compose up --no-deps -d frontend`

* To only update nginx `default` file
    * `nano /app/source/nginx/default`
    * `cd /app/source && docker-compose build nginx && docker-compose up --no-deps -d nginx`

* Get postgres data
    * will be at `/app/pg-vols`

# Commands In More Detail
* Windows: Via docker quickstart terminal in directory containing docker-compose.yml
* Linux: just use terminal in directory containing docker-compose.yml

* When updating entire thing
    * `docker-compose build`
        * builds images out of all the Dockerfiles mentioned in docker-compose.yml
    * `docker-compose images`
        * see list of built images
    * `docker-compose up`
        * starts running containers from all relevant images and coordinating them
        * `-d` run detached in the background
    * `docker-compose ps`
        * see list of running containers
    * `docker-compose stop`
        * stop containers from running
    * `docker-compose down`
        * stop & delete containers, delete images
    * `docker-compose logs`

* When updating only a specific services
    * First, upload the updated code for that specific service
        * Best way to do this is via Git to only update the parts that need changing
        * Don't forget, backend's Dockerfile is set up to build so you only need to update the source code
    * `docker-compose build [service name]`
        * build images of specific service, use when updating only that one
    * `docker-compose up --no-deps -d [service name]`
        * recreate and restart only a specific service


# Misc
* PostgreSQL config
    * See postgres/Dockerfile to set up default db name, username, and password
        * Note database is not exposed on the outside so don't complicate too much
    * Connection string to use
        `User Id=postgres; Database=postgres; Password=supersecretpassword; Host=db; Port=5432;`

* PostgreSQL volumes
    * the volumes contain postgresql's data, config, and logs
    * they are sync'd from the docker container to the local host machine
    * should persist even if container delete
    * @ `/app/pg-vols` @ local machine (host)

* PostgreSQL auto backups
    * TODO: use this command with cron do generate auto backups, also make rotating so dumps don't accumulate
        * ``pg_dumpall -c  | gzip > /app/pg_backups/dump_`date +%d-%m-%Y"_"%H_%M_%S`.gz``

# TODO
* create a better nginx file @ nginx/default
* postgresql auto backups

