Noia Node guide on Ubuntu using terminal only UPDATED 2019-08-11
==================

This is a quick tutorial for those wanting to host a NOIA node on a virtual box for example. I have used Vultr for this purpose and setup a Ubuntu 16.04 VM.

If you are interested in signing up on Vultr too, you could use my [ref link](https://www.vultr.com/?ref=7436414) to sign up. Otherwise just browse to [Vultr](https://www.vultr.com) and sign up.

If you need help setting up a VM, contact me here.

Register and KYC on the NOIA platform
-------------
[Register here](https://dashboard.noia.network/r/14b984b1)

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

If you want to use globally.

**NOTICE**: You need administrator rights to install globally.

```
$ npm install @noia-network/node-cli -g
```

**NOTICE**: If installation fails with administrator rights, then try suppressing the UID/GID switching when running the install command:

```
$ npm install @noia-network/node-cli -g --unsafe-perm
```

For the FIRST TIME, you need to define master address. It will generate `node.settings` with defined master address.

```sh
$ noia-node-cli --masterAddress wss://csl-masters.noia.network:5565
```

or environment variable

```sh
NOIA_NODE_MASTER_ADDRESS=wss://csl-masters.noia.network:5565 noia-node-cli
```

Noia node configuration
-------------

Find the node.settings file on your system using 
```
find / -type f -name "node.settings"
```

For me it was sitting in 
```
/root/.noia-node/node.settings
```

Open the file using vi
```
vi /root/.noia-node/node.settings
```

and edit the masterAddress to match the following:
```
master-address=wss://csl-masters.noia.network:5565
```

Also edit the airdropAddress if you want to receive the node rewards :).
Make sure to input your own ERC20 Wallet!!
```
airdropAddress=0xfe1ADFa5d31d3F7da12465d1A56823Cd68C03188
```

Running NOIA
-------------

You can now try the following
```
noia-node-cli start
```
It should output something like the following:
```
info: [noia-node] Initializing NOIA node, settings-path=/root/.noia-node/node.settings.
info: [noia-node] Storage dir=/root/.noia-node/storage, allocated=104857600.
(node:480) MaxListenersExceededWarning: Possible EventEmitter memory leak detected. 11 change listeners added. Use emitter.setMaxListeners() to increase limit
info: [noia-node] NOIA node initialized.
info: [noia-node] Connecting to master (without blockchain), master-address=wss://csl-masters.noia.network:5565.
```

Adding noia node as a service (Important if you want to close the terminal you are working in)
-------------

Create a file called noia-node.service in the directory: /etc/systemd/system, adjust its execution rights ```chmod 664 /etc/systemd/system/noia-node.service``` and restart the systemctl daemon using: ```systemctl daemon-reload```

Copy the following contents to your service file and ajust the ExecStart and WorkinDirectory paths to your node and noia installation paths. In my case:
```
[Unit]
Description=NOIA Node

[Service]
ExecStart=/usr/bin/node /usr/local/lib/node_modules/@noia-network/node-cli/dist/index.js
Restart=always
User=root
Group=root
Environment=PATH=/usr/bin:/usr/local/bin
Environment=NODE_ENV=production
WorkingDirectory=/usr/local/lib/node_modules/@noia-network/node-cli
Environment=NOIA_NODE_USER_DATA_PATH=/root/.noia-node

[Install]
WantedBy=multi-user.target
```

Restart the deamon
```
systemctl daemon-reload
systemctl enable noia-node.service
systemctl start noia-node.service
```

Check if everything works
------------

To check whether the node is running type
    
    systemctl | grep noia

You should see something like this:

    noia-node.service  loaded  active  running NOIA Node
    
Also you need to check this:
```
sudo systemctl status noia-node.service
```

If noia is running successfull, it returns something like this:
```
root@vultr:~# sudo systemctl status noia-node.service
● noia-node.service - NOIA Node
   Loaded: loaded (/etc/systemd/system/noia-node.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-07-18 09:46:50 UTC; 3 weeks 3 days ago
```
 

Troubleshooting
------------
Try running the following to get more information on the possible error:
```
journalctl -u noia-node.service
```

You can also try:
```
sudo systemctl status noia-node.service
```

If noia is running successfull, it returns something like this:
```
root@vultr:~# sudo systemctl status noia-node.service
● noia-node.service - NOIA Node
   Loaded: loaded (/etc/systemd/system/noia-node.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-07-18 09:46:50 UTC; 3 weeks 3 days ago
```

Try setting in the node.settings file
```
natpmp=true
```

Old part of the tutorial
------------
I created a new directory below my home directory. You can create it anywhere you like though. I will be using the path: /home/noia in this tutorial.

    git clone https://github.com/noia-network/noia-node-terminal.git
    cd /home/noia/noia-node-terminal

This is enough to test the installation using:

    npm install
    npm run build
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

Adjust the ExecStart and WorkinDirectory paths to your node and noia installation paths. See an example of my service file witin this repo.

Once done, copy the service file to your /etc/systemd/system directory, adjust its execution rights and restart the systemctl daemon.

    cp /home/noia/noia-node-terminal/noia-node.service /etc/systemd/system/
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
