Oracle WebLogic Server on Docker
=================================
These  Docker configurations have been used to create the Oracle WebLogic Server image. Providing this WLS image facilitates the configuration and environment setup for DevOps users. This project includes the installation and creation of an empty WebLogic Server domain (an Administration Server only). These Oracle WebLogic Server 12.2.1.3 images are based on Oracle Linux and Oracle JRE 8 (Server).

The certification of Oracle WebLogic Server on Docker does not require the use of any file presented in this repository. Customers and users are welcome to use them as starters, and customize, tweak, or create from scratch, new scripts and Dockerfiles.

For more information on the certification, please see the [Oracle WebLogic Server on Docker certification whitepaper](http://www.oracle.com/technetwork/middleware/weblogic/overview/weblogic-server-docker-containers-2491959.pdf) and [The WebLogic Server Blog](https://blogs.oracle.com/WebLogicServer/) for updates.

## How to build and run
This project offers sample Dockerfiles for Oracle WebLogic Server 12cR2 (12.2.1.3). It provides at least one Dockerfile for the 'developer' distribution, a second Dockerfile for the 'generic' distribution, and a third Dockerfile for the 'infrastructure' distribution. To assist in building the images, you can use the [`buildDockerImage.sh`](dockerfiles/buildDockerImage.sh) script. See below for instructions and usage.

The `buildDockerImage.sh` script is a utility shell script that performs MD5 checks and is an easy way for beginners to get started. Expert users are welcome to directly call `docker build` with their prefered set of parameters.


### Building Oracle WebLogic Server Docker install images
**IMPORTANT:** You must download the binary of Oracle WebLogic Server and put it in place (see `.download` files inside `dockerfiles/<version>`). The WebLogic image extends the Oracle JRE Server 8 image. You must either build the image by using the Dockerfile in [`../../../OracleJava/java8`](https://github.com/oracle/docker-images/tree/master/OracleJava/java-8) or pull the latest image from the [Oracle Container Registry](https://container-registry.oracle.com) or the [Docker Store](https://store.docker.com).

Before you build, select the version and distribution for which you want to build an image, then download the required packages (see `.download` files) and locate them in the folder of your distribution version of choice. Then, from the `dockerfiles` folder, run the `buildDockerImage.sh` script as root.

        $ sh buildDockerImage.sh
        Usage: buildDockerImage.sh -v [version] [-d | -g | -i] [-s]
        Builds a Docker Image for Oracle WebLogic Server.

        Parameters:
           -v: version to build. Required.
           Choose : 12.2.1.3
           -d: creates image based on 'developer' distribution
           -g: creates image based on 'generic' distribution
           -i: creates image based on 'infrastructure' distribution
           -c: enables Docker image layer cache during build
           -s: skips the MD5 check of packages

        * select one distribution only: -d, -g, or -i

        LICENSE UPL 1.0

        Copyright (c) 2014-2018 Oracle and/or its affiliates. All rights reserved.

**IMPORTANT:** The resulting images will have an empty domain (Administration Server only), by default. You must extend the image with your own Dockerfile, and create your domain using WLST. You might take a look at the use case samples.

## Samples for Oracle WebLogic Server domain creation
To give users an idea of how to create a domain from a custom Dockerfile to extend the WebLogic Server image, we provide a few samples for 12c versions of the developer distribution. For an example, we provide samples to create a `12.2.1.3 medrec` domain.

### Sample installation and base domain for Oracle WebLogic Server 12.2.1.3
The image `oracle/weblogic:12.2.1.3-developer` will configure a `base_domain` with the following settings:

 * Admin Username: `weblogic`
 * Admin Password: Auto generated
 * Oracle Linux Username: `oracle`
 * Oracle Linux Password: `welcome1`
 * WebLogic Server Domain Name: `base_domain`
 * Admin Server on port: `7001`
 * Production Mode: `developer`

**IMPORTANT:** If you intend to run these images in production, you must change the Production Mode to `production`.


### Admin password

On the first startup of the container, a random password will be generated for the administration of the domain. You can find this password in the output line:

`Oracle WebLogic Server auto generated Admin password:`

If you need to find the password at a later time, grep for `password` in the Docker logs generated during the startup of the container.  To look at the Docker container logs, run:

        $ docker logs --details <Container-id>

### Write your own Oracle WebLogic Server domain with WLST
The best way to create your own, or extend domains, is by using the [WebLogic Scripting Tool](https://docs.oracle.com/middleware/1221/cross/wlsttasks.htm). You can find an example of a WLST script to create domains at [`create-wls-domain.py`](dockerfiles/12.2.1.3/container-scripts/create-wls-domain.py). You may want to tune this script with your own setup to create datasources and connection pools, security realms, deploy artifacts, and so on. You can also extend images and override an existing domain, or create a new one with WLST.

## Building the Oracle WebLogic Server Docker image
To try a sample of a WebLogic Server image with a base domain configured, follow the steps below:

  1. To build the `12.2.1.3`image, from `dockerfiles`, call:

        `$ sh buildDockerImage.sh -v 12.2.1.3-d`

  2. Verify that you now have this image in place with:

        `$ docker images`

  3. Start a container from the image created in step 1.
     You can override the default values of the following parameters during runtime with the `-e` option:
      * `ADMIN_NAME`     (default: `AdminServer`)
      * `ADMIN_PORT`     (default: `7001`)
      * `ADMIN_USERNAME` (default: `weblogic`)
      * `ADMIN_PASSWORD` (default: Auto Generated)
      * `DOMAIN_NAME`    (default: `base_domain`)
      * `DOMAIN_HOME`    (default: `/u01/oracle/user_projects/domains/base_domain`)

**NOTE**: To set the `DOMAIN_NAME`, you must set both `DOMAIN_NAME` and `DOMAIN_HOME`.

        $ docker run -d -p 7001:7001 -e ADMIN_USERNAME=weblogic -e ADMIN_PASSWORD=welcome1 -e DOMAIN_HOME=/u01/oracle/user_projects/domains/abc_domain -e DOMAIN_NAME=abc_domain oracle/weblogic:12.2.1.3-developer

  4. Run the Administration Console:

        `$ docker inspect --format '{{.NetworkSettings.IPAddress}}' <container-name>`

        This returns the IP address of the container (for example, `xxx.xx.x.x`). Go to your browser and enter `http://xxx.xx.x.x:7001/console`


## Choose your Oracle WebLogic Server distribution
This project hosts two configurations (depending on the Oracle WebLogic Server version) for building Docker images with WebLogic Server 12c.

 * Quick Install Developer Distribution

   - For more information on the Oracle WebLogic Server 12cR2 Quick Install Developer Distribution, see [WLS Quick Install Distribution for Oracle WebLogic Server 12.2.1.3.0](http://download.oracle.com/otn/nt/middleware/12c/wls/12213/README.txt).


 * Generic Distribution

   - For more information on the Oracle WebLogic Server 12cR2 Generic Full Distribution, see [WebLogic Server 12.2.1.3 Documentation](http://docs.oracle.com/middleware/12213/wls/index.html).

## Copyright
Copyright (c) 2014-2018 Oracle and/or its affiliates. All rights reserved.
