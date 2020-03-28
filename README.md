# BuildTemplates

Reusable Azure Pipelines Build Templates used to deploy containerized bots

# How to use

In another project, in your YAML build (`azure-pipelines.yml`), configure your build to use this template, see below for a working YAML example.

You need to create the following services:

-   An SSH Endpoint (named `Google Cloud Bots` in the example)
-   A Docker Registry endpoint (named `Docker Hub` in the example)
-   Have a secure environment file to read secrets from when running the container (named `ogechobot.env` in the example)
-   A git repository endpoint to pull the templates from (named `for-the-new-order` in the example)

```yaml
trigger:
    branches:
        include:
            - master

pool:
    name: Hosted Ubuntu 1604

variables:
    bot.image: 'carlhugo/ogechobot'
    bot.imageName: 'ogechobot'
    bot.sshEndpoint: 'Google Cloud Bots'
    bot.dockerRegistryEndpoint: 'Docker Hub'
    bot.envFile: 'ogechobot.env'

resources:
    repositories:
        - repository: shared
          type: github
          name: for-the-new-order/BuildTemplates
          endpoint: for-the-new-order

jobs:
    - template: BuildAndDeployBot.yml@shared
      parameters:
          bot:
              image: $(bot.image)
              imageName: $(bot.imageName)
              sshEndpoint: $(bot.sshEndpoint)
              dockerRegistryEndpoint: $(bot.dockerRegistryEndpoint)
              envFile: $(bot.envFile)
          buildNumber: $(Build.BuildNumber)
```
