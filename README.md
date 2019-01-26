## Using JETSCAPE via Docker

Docker is a software tool that allows one to deploy an application in a portable environment. 
A docker "image" can be created for the application, allowing any user to run a docker "container" from this image.
We have prepared docker images for the JETSCAPE environment, which allow you to use JETSCAPE on macOS or linux without
installing a long list of pre-reqs or worrying about interference with software you already have installed.

### Step 1: Install Docker

#### macOS

1. Install Docker Desktop for Mac: https://docs.docker.com/docker-for-mac/install/
2. Open Docker, go to Preferences --> Advanced and 
    - (i) Set CPUs to max that your computer has (`sysctl -n hw.ncpu`),
    - (ii) Set memory to what you are willing to give Docker.

#### linux

1. Install Docker: https://docs.docker.com/install/
2. Allow your user to run docker (requires admin privileges): 
    ```
    sudo groupadd docker
    sudo usermod -aG docker $USER
    ```
    Log out and log back in.

### Step 2: Run JETSCAPE

#### Option 1: JETSCAPE pre-req environment (default)

1. Make a directory on your machine (which will be shared with the docker container), and clone JETSCAPE into it. 
    ```
    mkdir ~/jetscape-user
    cd ~/jetscape-user
    git clone git@github.com:JETSCAPE/JETSCAPE.git
    ```

2. Start a docker container that contains all of the JETSCAPE pre-reqs: 

    ```
    docker run -it -v ~/jetscape-user:/home/jetscape-user --name jetscapeContainer jdmulligan/jetscape-base:v1test
    ```

    This is what the `docker run` command does:
    - `docker run` starts the docker container from a pre-defined image jdmulligan/jetscape-base:v1test, which will be downloaded if necessary.
    - `-it` runs the container with an interactive shell.
    - `-v` mounts a shared folder between your machine (at ~/jetscape-user) and the container (at /home/jetscape-user/shared), through which you can transfer files to and from the container. You can edit the locations as you like.
    - `--name` (optional) sets a name for your container, for convenience.

3. Build JETSCAPE as usual:
    ```
    cd JETSCAPE
    mkdir build
    cd build
    cmake ..
    make -j4
    ```

*That's it!* You are now inside the docker container, with JETSCAPE and all of its prequisites installed. You can run JETSCAPE executables or edit and re-compile code. Moreover, since we set up the jetscape-user folder to be shared between your host and the docker container, you can do text-editing etc. on your host machine, and then immediately build JETSCAPE in the docker container.

When you generate output files, you should then pass these through the shared folder to your personal machine for analysis 
(the docker container doesn't contain ROOT or other analysis tools -- only what is necessary to produce output files from JETSCAPE).

Some useful commands:
- To see the containers you have running, and get their ID: `docker container ls`
- To stop the container: `docker stop <container>` or `exit`
- To re-start the container: `docker start <container>`
- To put a running container into detatched mode: `Ctrl-p Ctrl-q`, and to re-attach: `docker attach <container>` 

#### Option 2: Full JETSCAPE environment

Instead, you may wish to run a docker container that already has JETSCAPE pre-compiled. This is convenient if you wish to deploy JETSCAPE onto an external machine for production purposes rather than testing/development.

1. Make a directory on your host machine, to be shared with the docker container. For example:

    ```
    mkdir ~/jetscape-user
    ```

2. Start the docker container: 

    ```
    docker run -it -v ~/jetscape-user:/home/jetscape-user/shared --name jetscapeContainer jdmulligan/jetscape-deploy:v1test
    ```

*That's it!* You are now inside the docker container, with JETSCAPE and all of its prequisites pre-installed. You can pass files from your machine to the docker container through the shared folder (~/jetscape-user on your machine, /home/jetscape-user/shared in the docker container).

========================================================================================

If you have any issues or suggestions, please send a mail to james.mulligan@berkeley.edu.
