# docker-amazon-ask-cli

ASK documentation: https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html#step-2-install-and-initialize-ask-cli


https://developer.amazon.com/docs/smapi/skill-manifest.html#sslCertificateType-enumeration

```bash
# TODO how to do this from URL instead so no-pull required
docker build -t ask-cli .

docker build https://raw.githubusercontent.com/martindsouza/docker-amazon-ask-cli/master/Dockerfile

# https://docs.docker.com/docker-hub/repos/#pushing-a-repository-image-to-docker-hub
docker login
docker build -t martindsouza/amazon-ask-cli:0.0.1 .

docker push martindsouza/amazon-ask-cli


# tODO: remove the rm just for testing

# TOdO volume for ~/.ask/cli_config

# TODO change volume location
# docker run -itd \
#   --name askcli \
#   --volume $(pwd)/ask-config:/home/node/.ask \
#   --volume $(pwd)/app-dev:/home/node/app-dev \
#   ask-cli:latest


# Run command on demand

# Option 1: Run bash and "keep open"
docker run -it --rm \
  -v $(pwd)/ask-config:/home/node/.ask \
  -v $(pwd)/app-dev/HelloWorld:/home/node/app-dev \
  ask-cli:latest \
  bash

# Option 2: Run on demand
docker run -it --rm \
  -v $(pwd)/ask-config:/home/node/.ask \
  -v $(pwd)/app-dev/HelloWorld:/home/node/app-dev \
  ask-cli:latest \
  ask deploy -t skill

  
  ask deploy -t model

  ask new --skill-name HelloWorld


  ask init -l # Command to run

# List all configs
docker exec -ti askcli bash -c "ask init -l"  

# List config files
docker exec -ti askcli bash -c "cd /home/node/.ask && pwd && ls -la"

# Configure
docker exec -ti askcli bash -c "ask init --no-browser"

# Bash command
docker exec -it askcli bash
```

