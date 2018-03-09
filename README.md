# docker-amazon-ask-cli

The purpose of this container is to be able to use the [Amazon ASK CLI (Alexa Skills Kit)](https://developer.amazon.com/docs/smapi/ask-cli-intro.html#alexa-skills-kit-command-line-interface-ask-cli).

<!-- TOC -->

- [docker-amazon-ask-cli](#docker-amazon-ask-cli)
  - [Example](#example)
  - [AWS Config (optional)](#aws-config-optional)
    - [`aws configure`](#aws-configure)
    - [Setting `credentials` and `config` file](#setting-credentials-and-config-file)
  - [Usage](#usage)
    - [Run One Command](#run-one-command)
    - [Run `bash`, then run `ask`](#run-bash-then-run-ask)
    - [Volumes](#volumes)
  - [Developers](#developers)
  - [Links:](#links)

<!-- /TOC -->

## Example

This example will show you both how to use this container and start a simple `HelloWorld` Alexa Skill. In this example we'll assume that AWS has been configured [see below](#aws-config-optional). 

Please read the other sections on how to properly use this container and volume configurations. Before running this example ensure that you've registered for an [Alexa Developer](https://developer.amazon.com/alexa) account


```bash
# Get image
docker pull martindsouza/amazon-ask-cli

# Create a ASK configuration folders.
cd ~
mkdir alexa-demo \
  alexa-demo/ask-config \
  alexa-demo/aws-config \
  alexa-demo/app

# Note copy the aws-config information (see below) to the aws-config folder

# To help simply writing out a full docker run command each time will use an alias
alias alexa="docker run -it --rm \
  -v ~/alexa-demo/ask-config:/home/node/.ask \
  -v ~/alexa-demo/aws-config:/home/node/.aws \
  -v ~/alexa-demo/app:/home/node/app \
  martindsouza/amazon-ask-cli:latest "

# For windows users you'll need to run the following each time (unless you have an alternative to alias)
# docker run -it --rm \
#   -v ~/alexa-demo/ask-config:/home/node/.ask \
#   -v ~/alexa-demo/aws-config:/home/node/.aws \
#   -v ~/alexa-demo/app:/home/node/app \
#   martindsouza/amazon-ask-cli:latest \
#   <command> 


# Configure ASK
alexa ask init --no-browser

# ? Please choose one from the following AWS profiles for skill's Lambda function deployment.
#  default
# Chose default and hit enter
#
# A URL will be printed on screen. Copy and past into your browser
# Login using your Amazon Developer account
# Copy the code that is shown on the screen and past in the terminal
# You should see a success message like: Tokens fetched and recorded in ask-cli config.


# Verify profile was created
alexa ask init -l
# Should return:
# Profile              Associated AWS Profile
# [default]                 ** NULL **


# Create a new app:
alexa ask new --skill-name HelloWorld
# New project for Alexa skill created.
# This will also create a new folder: ~/alexa-demo/HelloWorld


# To help simplify things, map ~/alexa-demo/app/HelloWorld to the /home/node/app folder
# Note: if we find a better way this will change in the future
alias alexa="docker run -it --rm \
  -v ~/alexa-demo/ask-config:/home/node/.ask \
  -v ~/alexa-demo/aws-config:/home/node/.aws \
  -v ~/alexa-demo/app/HelloWorld:/home/node/app \
  martindsouza/amazon-ask-cli:latest "

# Deploy (all): this will create both lambda and Alexa Skill
alexa ask deploy

# -------------------- Create Skill Project --------------------
# Profile for the deployment: [default]
# Skill Id: amzn1.ask.skill.55cb6762-4b48-433f-adef-2d6074d06c13
# Skill deployment finished.
# Model deployment finished.
# Lambda deployment finished.
# Your skill is now deployed and enabled in the development stage.
# Try invoking the skill by saying “Alexa, open {your_skill_invocation_name}” or simulate an invocation via the `ask simulate` command.


# Other options are:
# alexa ask deploy -t lambda
# alexa ask deploy -t skill
# alexa ask deploy -t model


```

## AWS Config (optional)
If you plan to use [Lambda](https://aws.amazon.com/lambda/) you'll need to configure the AWS CLI. To simplify. You can configure it multiple ways.

In either case ensure that you pass in `-v $(pwd)/aws-config:/home/node/.aws \` (where `$(pwd)/aws-config` is a location on your host machine) as an option when running the container to preserve the AWS configuration.

### `aws configure`

Running `aws configure` in the container will ask you a set of questions to create the AWS credentials

### Setting `credentials` and `config` file

Copy the files in [`samples/aws-config`](samples/aws-config) to your local/host folder that will hold the credentials. You need to modify the `credentials` file with your `aws_access_key_id` and `aws_secret_access_key` at a minimum.

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
`/home/node/.ask` | `.ask` configuration folder for ASK cli
`/home/node/.aws` | `.aws` configuration folder AWS cli 
`/home/node/app` | folder where your Alexa project is stored


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

