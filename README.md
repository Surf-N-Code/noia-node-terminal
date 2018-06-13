Noia Node guide on Ubuntu using terminal only
==================

This is a quick tutorial for those wanting to host a NOIA node on a virtual box for example. I have used Vultr for this purpose and setup a Ubuntu 16.04 VM.

If you are interested in signing up on Vultr too, you could use my [ref link]() https://www.vultr.com/?ref=7436414) to sign up. Otherwise just browse to [Vultr](https://www.vultr.com and sign up).

If you need help setting up a VM, contact me here.

Initial system checks and requirements
-------------
We need to make sure that systemd is being used for managing deamons. Run the following command. If your system is using upstart, please refer to this great [tutorial]( https://www.digitalocean.com/community/tutorials/the-upstart-event-system-what-it-is-and-how-to-use-it)

    ps -p 1 -o comm=

If you haven't got one already, create an ERC20 wallet here: [MEW](https://www.myetherwallet.com/)

Node JS and NPM are required for the noia node to run. Install using the commands below
    curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
    sudo apt-get install -y nodejs
    sudo apt-get install build-essential

Noia node installation
------------
I created a new directory below my home directory. You can create it anywhere you like though. I will be using the path: /home/noia in this tutorial.

    git clone https://github.com/noia-network/noia-node-terminal.git
    cd /home/noia/noia-node-terminal

This is enough to test the installation using:

    npm start

A couple of things will happen, in the end you should get an error though. Something like this:

    [NODE]: initialized.
    [NODE]: starting...
    /home/noia/noia-node-terminal/node_modules/noia-node/dist/lib/master.js:66
                throw new Error("master address null or undefined");
                ^

    Error: master address null or undefined
        at Master.connect (/home/noia/noia-node-terminal/node_modules/noia-node/dist/lib/master.js:66:19)
        at Node.start (/home/noia/noia-node-terminal/node_modules/noia-node/dist/index.js:74:25)
        at Object.<anonymous> (/home/noia/noia-node-terminal/index.js:74:6)
        at Module._compile (internal/modules/cjs/loader.js:702:30)
        at Object.Module._extensions..js (internal/modules/cjs/loader.js:713:10)
        at Module.load (internal/modules/cjs/loader.js:612:32)
        at tryModuleLoad (internal/modules/cjs/loader.js:551:12)
        at Function.Module._load (internal/modules/cjs/loader.js:543:3)
        at Function.Module.runMain (internal/modules/cjs/loader.js:744:10)
        at startup (internal/bootstrap/node.js:238:19)

If you get a different error, you need to debug - make sure latest npm and nodejs versions are installed

Noia node configuration
-------------

Having run the command above, should have generated a settings.json file within your noia-node-terminal folder. Open that file and adjust a couple of settings as described below:

    nano settings.json

Adjust the line for masterAddress to match this:

    "masterAddress": "ws://csl-masters.noia.network:5565"

Adjust the line for wallet.address to match this:

    "wallet.address": "Your ERC20 wallet address"

Test the node again by running
    npm start

Now you should some logging of [info] message about your configuration such as this line:

    info: [noia-node] Creating wire... interface=gui, node_ip=yourip, node_ws_port=7676, node_domain=, node_wallet_address=your ETH address

You should not see any error messages at this point. If you do, please make sure you have executed all steps from above correctly. Theoretically you can now let this run, but we want the node to be running as a service in the background and startup automatically when the machine reboots for example.

Adding noia node as a service
-------------
Open the noia-node.service file provided within your installation path

    nano noia-node.service

Adjust the ExecStart and WorkinDirectory paths to your node and noia installation paths

Once done, copy the service file to your /etc/systemd/system directory, adjust its execution rights and restart the systemctl daemon.

    cp /home/noia/noia/noia-node-terminal/noia-node.service /etc/systemd/system/
    chmod 664 /etc/systemd/system/noia-node.service
    systemctl daemon-reload
    systemctl enable noia-node.service
    systemctl start noia-node.service

Check if everything works
------------

To check whether the node is running type
    systemctl | grep noia

You should see something like this:

    noia-node.service  loaded  active  running NOIA Node
