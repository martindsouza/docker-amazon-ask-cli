# docker-amazon-ask-cli

The purpose of this container is to be able to use the [Amazon ASK CLI (Alexa Skills Kit)](https://developer.amazon.com/docs/smapi/ask-cli-intro.html#alexa-skills-kit-command-line-interface-ask-cli).

<!-- TOC -->

- [docker-amazon-ask-cli](#docker-amazon-ask-cli)
  - [Example](#example)
  - [Usage](#usage)
    - [Run One Command](#run-one-command)
    - [Run `bash`, then run `ask`](#run-bash-then-run-ask)
    - [Volumes](#volumes)
  - [Developers](#developers)
  - [Links:](#links)

<!-- /TOC -->

**Note: This doesn't work right now as tokens aren't being generated. Still looking into the issue**

## Example

This example will show you both how to use this container and start a simple `HelloWorld` Alexa Skill. Please read the other sections on how to properly use this container and volume configurations. Before running this example ensure that you've registered for an [Alexa Developer](https://developer.amazon.com/alexa) account

```bash
# Get image
docker pull martindsouza/amazon-ask-cli

# Create a ASK configuration folder.
cd ~
mkdir ask-config
mkdir alexa-apps

ASK_CONFIG=~/ask-config
ASK_APP=~/alexa-apps

docker run -it --rm \
  -v $ASK_CONFIG:/home/node/.ask \
  -v $ASK_APP:/home/node/app-dev \
  martindsouza/amazon-ask-cli:latest \
  bash

# This will open Bash in the container.

# Configure ASK
ask init --no-browser
# There is no AWS credential setup yet, do you want to continue the initialization?: Answer Y
# A URL will be printed on screen. Copy and past into your browser
# Login using your Amazon Developer account
# Copy the code that is shown on the screen and past in the terminal

# Verify profile was created
ask init -l

# Should return:
  # Profile              Associated AWS Profile
  # [default]                 ** NULL **

# Create a new app:
ask new --skill-name HelloWorld
# New project for Alexa skill created.
# This will also create a new folder: ~/alexa-apps/HelloWorld

# At this point we need to exit and re-run so we can set the project (HellowWorold) folder (easier)
exit


ASK_APP=~/alexa-apps/HelloWorld

docker run -it --rm \
  -v $ASK_CONFIG/ask-config:/home/node/.ask \
  -v $ASK_APP:/home/node/app-dev \
  martindsouza/amazon-ask-cli:latest \
  bash

# Deploy Skill
ask deploy -t skill
```

## Usage

Get the latest version of the container: `docker pull martindsouza/amazon-ask-cli`

They're two ways to use the `ask` cli for this container which are covered below. The volume documentation is listed following the examples.

### Run One Command

In this mode the container will start, you run your `ask` command, then the container is stopped and deleted. _Don't worry your Docker image is still kept._

```bash
docker run -it --rm \
  -v $(pwd)/ask-config:/home/node/.ask \
  -v $(pwd)/hello-world:/home/node/app-dev \
  martindsouza/amazon-ask-cli:latest \
  ask init -l
```

### Run `bash`, then run `ask`

In this mode, the container will start, you can then run the container's bash, and the container will stop and delete only once you `exit`.

```bash
docker run -it --rm \
  -v $(pwd)/ask-config:/home/node/.ask \
  -v $(pwd)/app-dev/HelloWorld:/home/node/app-dev \
  martindsouza/amazon-ask-cli:latest \
  bash

# You'll be prompted with:
# bash-4.3$
#
# Type: exit  to end and terminate the container
```


### Volumes

Path | Description 
--- | ---
`/home/node/.ask` | `.ask` configuration folder
`/home/node/app-dev` | folder where your Alexa project is stored


## Developers

```bash
docker build -t martindsouza/amazon-ask-cli .

# Pushing to Docker Hub
# Note: not required since I have a build hook linked to the repo
# docker login
# docker build -t martindsouza/amazon-ask-cli:0.0.1 .
# docker push martindsouza/amazon-ask-cli

```

## Links:

- [ASK CLI Quickstart](https://developer.amazon.com/docs/smapi/quick-start-alexa-skills-kit-command-line-interface.html)
- [ASK CLI Full Doc](https://developer.amazon.com/docs/smapi/ask-cli-intro.html#alexa-skills-kit-command-line-interface-ask-cli)


https://developer.amazon.com/docs/smapi/skill-manifest.html#sslCertificateType-enumeration
