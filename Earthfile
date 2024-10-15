VERSION 0.8
# This follows the instructions here:
# https://docs.midnight.network/develop/tutorial/building/
compile:
    #
    # Prerequisits
    #
    FROM --platform=linux/amd64 ubuntu
    ENV EXAMPLES_VERSION=0.1.14
    ENV COMPACT_VERSION=0.18.2

    RUN apt-get update -qq \
     && apt-get upgrade -y -qq
    RUN apt-get install -y -qq curl

    RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
    ENV NVM_DIR="$HOME/.nvm"
    # After following the NVM installation instructions, verify that NVM is installed:
    # (Docker Note: 'nvm' is a script, this is a way to auto-source it.)
    RUN . $NVM_DIR/nvm.sh && nvm --version
    # You should see a version number printed, such as 0.39.5.

    # Install LTS version of Node 18x or greater:
    RUN . $NVM_DIR/nvm.sh && nvm install 18 --lts

    RUN apt-get install -y -qq wget
    RUN wget https://d3fazakqrumx6p.cloudfront.net/artifacts/compiler/compactc_$COMPACT_VERSION/compactc-linux.zip
    RUN apt-get install -y -qq unzip

    # Create a directory in which to place the compiler and its supporting binaries, 
    # and unzip the file inside that directory. For example:
    RUN mkdir --parents ~/my-binaries/compactc
    RUN mv ./compactc-linux.zip ~/my-binaries/compactc/
    RUN cd ~/my-binaries/compactc && unzip ./compactc-linux.zip

    # On Linux, or Windows with WSL, the unzipped compiler should already be executable,
    # but if you run into problems, check that compactc.bin, zkir, and compactc are executable,
    # or run chmod +x compactc.bin zkir compactc in the unzipped Compact compiler directory.
    #
    # While still in the directory where you unzipped the compiler, 
    # you can check that it runs with the command:
    RUN cd ~/my-binaries/compactc && ./compactc --version && pwd

    # The Midnight example DApps use the shell environment variable COMPACT_HOME to locate the Compact compiler.
    # You may want to add this to your shell profile. For example:
    ENV COMPACT_HOME="/root/my-binaries/compactc/"

    # In order to run Midnight Compact compiler add the directory where the compiler is to your $PATH environment, 
    # reload your shell config file and check if Midnight Compact compiler runs with the command:
    ENV PATH=$PATH:/root/my-binaries/compactc/
    RUN compactc --version

    #
    # Midnight examples
    #
    RUN wget https://raw.githubusercontent.com/midnight-ntwrk/releases/gh-pages/artifacts/examples/midnight-examples-$EXAMPLES_VERSION.zip
    RUN unzip midnight-examples-$EXAMPLES_VERSION.zip
    WORKDIR /midnight-examples-$EXAMPLES_VERSION

    # In the Midnight examples directory, there is a file .nvmrc which will configure NVM correctly for the purposes of this tutorial.
    # Verify that your current directory is the midnight-examples-x.y.z directory, and run the following command:
    RUN . $NVM_DIR/nvm.sh && nvm install

    # You will be using Yarn to build Midnight example DApps.
    # An easy way to install Yarn, now that you have Node configured, is with the corepack command:
    RUN . $NVM_DIR/nvm.sh && npm i -g corepack && corepack enable

    # THIS WASN'T SPECIFIED IN THE DOCS but seemed needed to upgrade to latest version:
    RUN . $NVM_DIR/nvm.sh && npm install -g npm

    # This succeeds silently, but you should see version information now if you type:
    RUN . $NVM_DIR/nvm.sh && yarn --version

    # The best way to verify this step is to use Yarn to pull the Midnight examples dependencies.
    # In the top level of your midnight-examples folder, simply invoke Yarn with no parameters:
    RUN . $NVM_DIR/nvm.sh && yarn

    #
    # Example: Counter build
    #
    WORKDIR /midnight-examples-$EXAMPLES_VERSION/examples/counter

    # Build both sub-projects at once by running the following command in the counter directory above the two sub-directories:
    # RUN . $NVM_DIR/nvm.sh && npx turbo build
    RUN . $NVM_DIR/nvm.sh && npx turbo build

    # WORKDIR /midnight-examples-$EXAMPLES_VERSION/examples/counter/counter-cli
    # RUN . $NVM_DIR/nvm.sh && yarn testnet-remote

    # RUN . $NVM_DIR/nvm.sh && npx turbo test
