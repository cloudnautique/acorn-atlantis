# Atlantis Acorn

## What is this?

This is an Acornfile for to run the [Atlantis](https://www.runatlantis.io/) Terraform automation tool within Acorn platform. This is a first pass at deploying the server allowing for GitHub PAT or GitHub App authentication. You can also provide server and repo configuration values along with setting secret values via environment variables.

## How do I use it?

These instructions are to get up and running, but you should read the Atlantis documentation to ensure you understand how to use it and configure properly for your use case.

For reference, see the examples folder with sample deployment Acornfiles that configure Atlantis.

### GitHub personal access token (PAT) authentication

The default is to run with GitHub PAT, To get started you will need to first create a secret with your GitHub auth info. You can do this via the Acorn CLI.

You will need a GitHub username and PAT. You will also need a randomly generated webhook secret string.

From the CLI you run the following command:

```shell
acorn secret create github --data username=${GITHUB_USERNAME} --data password=${GITHUB_PAT} --data webhookSecret=${GITHUB_WEBHOOK_SECRET}
```

```

This will create a secret named `github` with your GitHub username and PAT. You can then reference this secret when starting the server.

```shell
acorn run -n atlantis -s github:github ghcr.io/acorn-io/atlantis:v0.26.0-# --repoAllowList 'github.com/my-org/*'
```

This will setup the minimum to interact with GitHub, and will be accessible via the URL printed by Acorn CLI.

You can now setup webhooks for repos you want to use with Atlantis. The webhook URL will be the URL printed by the Acorn CLI with `/events` appended to the end.

### Atlantis Authentication

By default the Atlantis web app is protected by basic auth. The username are randomly generated by default. To learn the username and password you can run the following command:

```shell
acorn secret reveal atlantis.atlantis-creds
```

Assuming you launched the app with `-n atlantis` otherwise, replace the name `atlantis` before the `.` with the name of your Acorn instance.

### Additional configuration

You can pass additional configuration to the Atlantis server via the `--serverConfigYaml`, `--repoConfigYaml`, and credentials via environment variables using the `envconfig` secret. The values of the `envconfig` secret are added directly as environment variables to the container where `KEY=value`. The keys are injected as is.

The envconfig can be used to pass Atlantis configuration or values directly to TF.

```shell
acorn secrets create envconfig --data AWS_ACCESS_KEY=${AWS_ACCESS_KEY} --data AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY} --data AWS_REGION=${AWS_REGION} --data TF_VAR_XYS=blah
```

See the Examples folder for a GH App example.