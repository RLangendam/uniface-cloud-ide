# Uniface Cloud IDE

## Prerequisites

### Docker Desktop & Kubernetes

    choco install docker-desktop

Make sure to have Kubernetes enabled in your docker settings and use [WSL2](https://confluence.uniface.m4.local/display/TEAMF/Docker+Desktop+on+Windows+10#DockerDesktoponWindows10-WSL2).

### kubectl

    choco install kubernetes-cli

### Helm

    choco install kubernetes-helm

### chectl

    Set-ExecutionPolicy Bypass -Scope Process -Force; iex ((New-Object System.Net.WebClient).DownloadString('https://www.eclipse.org/che/chectl/win/'))

## Run

    chectl server:deploy --platform=docker-desktop --installer=helm

Make sure to install the self-signed certficate in your `Trusted Root Certification Authorities` by right-clicking the `cheCA.crt` file in `%LOCALAPPDATA%\Temp` and selecting `Install Certificate`.

Your Che environment should now be up an running within your local Docker desktop environment and can be accessed from your browser at [http://che-che.${CHE_DOMAIN}.nip.io](about:blank) where `CHE_DOMAIN` is the IP address of your machine as shown at end of the previous command output.

You may now create a new workspace from a devfile or the templates provided.

See also, [Running Eclipse Che on Kubernetes using Docker Desktop on macOS or Windows](https://medium.com/eclipse-che-blog/running-eclipse-che-on-kubernetes-using-docker-desktop-for-mac-5d972ed511e1)

## WIP: Build & Run with custom branding

First make sure a local docker registry is running

    docker run -d -p 5000:5000 --restart=always --name registry registry:2

Let's build our Uniface-branded version of Che.

    cd che
    docker build -t uniface/uniface-che:next .
    docker image tag uniface/uniface-che:next localhost:5000/uniface/uniface-che:next
    docker image push localhost:5000/uniface/uniface-che:next

Next, we need to build a custom version of the che plugin registry to publish a new `meta.yaml`

    cd che-plugin-registry
    docker build -t che-plugin-registry-build .
    docker run che-plugin-registry-build

> Todo as per https://www.eclipse.org/che/docs/che-7/contributor-guide/branding-che-theia/

Finally, we deploy Che with our branding

    chectl server:deploy --platform=docker-desktop --installer=helm --cheimage=localhost:5000/uniface/uniface-che:next

## Bootstrapping

This project revolves around creating a [devfile](https://www.eclipse.org/che/docs/che-7/end-user-guide/configuring-a-workspace-using-a-devfile/), a docker image (from a [Dockerfile](https://docs.docker.com/engine/reference/builder/)) and a project [seed](https://github.com/search?q=seed) repo, suitable for Uniface development. Both the docker image (to be hosted on a docker repository), the project seed and the procscript VSCode extension need to accessible to Che while bootstrapping the environment from the devfile.

A `devfile.yaml` has been generated by

    chectl devfile:generate --dockerimage=uniface-dev-env --git-repo=url-to-uniface-seed-project --name=uniface

but needs to be amended with

- A URL to the uniface project seed containing a project template and possibly a [helm chart](https://helm.sh/) for CI/CD.
- A docker image within a docker registry for the Uniface development environment.
- The Procscript plug-in for VSCode retrievable from a plugin registry.

Once we obtain the AWS K8s cluster information from the DevOps team we should be able to [install](https://www.eclipse.org/che/docs/che-7/installation-guide/installing-che-on-aws/) Che in the cloud.

Please have a look at the Contributor Guide in the [Che Documentation](https://www.eclipse.org/che/docs/che-7/overview/introduction-to-eclipse-che/) on how to change Che to our liking. E.g. through branding and with additional plugins.