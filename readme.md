Remote containers don't respect COMPOSE_PROJECT_NAME

Start the containers:

```
$ cd src
$ docker compose up

# in another terminal
$ docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED          STATUS          PORTS                  NAMES
5fecc27a3bbf   nginx:1.18-alpine   "/docker-entrypoint.…"   25 minutes ago   Up 25 minutes   0.0.0.0:9000->80/tcp   devcont-example-nginx-1
36c6e5d347ed   python:3.8          "sleep 365d"             25 minutes ago   Up 25 minutes                          devcont-example-backend-1

# compose is using project name "devcont-example" from .env file
```

Open folder `src/vscode` and click `Reopen in Container`, it'll fail to start because it'll
try to start the compose again with project name `src`. VSCode logs:

```
[1097 ms] Start: Run: docker -v
[1160 ms] Start: Run: docker events --format {{json .}} --filter event=start
[1165 ms] Start: Run: docker-compose --project-name src -f /home/david/work/devcont-compose-project/src/docker-compose.yaml build
[1389 ms] PersistedPath=/tmp, ContainerHasLabels=false
[1389 ms] Generating composeOverrideFile...
[1398 ms] Start: Run: docker inspect --type image python:3.8
```

And now:

```
$ docker ps
CONTAINER ID   IMAGE               COMMAND                  CREATED              STATUS              PORTS                  NAMES
00a49aa74c6b   python:3.8          "/bin/sh -c 'echo Co…"   About a minute ago   Up About a minute                          src-backend-1
5fecc27a3bbf   nginx:1.18-alpine   "/docker-entrypoint.…"   28 minutes ago       Up 28 minutes       0.0.0.0:9000->80/tcp   devcont-example-nginx-1
36c6e5d347ed   python:3.8          "sleep 365d"             28 minutes ago       Up 28 minutes                              devcont-example-backend-1

# vs code incorrectly tried to start again with project name "src", but failed to
# start the nginx container because another one already has a bind on port 9000
```