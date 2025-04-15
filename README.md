# Bitcoin Knots node + DATUM gateway configuration walkthrough

## Table of Contents

- [Creating A User](#creating-a-user)
- [Configuring Bitcoin](#configuring-bitcoin)
- [Configuring DATUM](#configuring-datum)
- [Monitoring Sync Progress](#monitoring-sync-progress)
- [Mining](#mining)
- [Checking the Dashboard](#checking-the-dashboard)
- [Modifying the Config Files](#modifying-the-config-files)

This guide will explain the various options presented to you in [this](https://github.com/Com320/OC-mech-datum-boxes) script which will be run during the installation methods presented [here](https://github.com/bitcoinmechanic/datum-guides).

**If you already have Bitcoin Knots and DATUM installed and just want to know what the various settings do, skip to [configuring bitcoin](#configuring-bitcoin) and [configuring datum](#configuring-datum).**

**Any of these settings can be changed after completing this script. Some basic configuration files can be trivially changed (i.e the secondary coinbase text in your DATUM gateway config used to label your OCEAN blocks) - others will be more fiddly, like the Debian user running your Bitcoin node and DATUM gateway.**

## Creating A User

(This will compile Bitcoin Knots and DATUM)

```
Setting up user...
Username from settings: bitcoin
Do you want to use this username? (y/n):
```

Your Bitcoin Knots node and your DATUM gateway will both be run from a new user created by this script. The default user that will be created is `Bitcoin` but you can use whatever you like.

**Bitcoin Knots will now be compiled at the latest release tag.**

```
Generating RPC authentication...
Generating RPC authentication details
This will create authentication credentials for Bitcoin RPC access
Enter RPC username (default: datumuser):
```

This sets up RPC authentication which DATUM needs to communicate with your Bitcoin node.

Whatever you enter here will be used to generate credentials using the rpcauth.py script found [here](https://github.com/bitcoinknots/bitcoin/blob/28.x-knots/share/rpcauth/rpcauth.py).

The relevant credentials will be inserted into your bitcoin.conf as appropriate and the generated password that gets inserted into the DATUM config later in the script will automatically be added too.

`Enter location for bitcoin.conf (default: /home/bitcoin/.bitcoin/bitcoin.conf): `

This file is the configuration file for the Bitcoin Knots node. Any changes you want to make to the way your node runs will likely require manually editing the file and restarting the node.

`Enter location for data (default: /home/bitcoin/.bitcoin/data): `

This is where the blockchain data will live along with a few other files.

## Configuring Bitcoin

`Enter value for 'prune' (default: 550): `

By default, we suggest pruning, however it is fine to enter `0` here if you do not wish to prune. DATUM does not require an archival node.

`Enter value for 'dbcache' (default: 100): `

This higher this value the faster your node will sync. (Unit = MB).

`Enter value for 'rpcauth' (default: datumuser:31368a1605d56dde8882fc6bc785037a$a5fd79c67a23d3db18128f7c675acb684bb05dffce479fa1ebc4a916834f4163):`

Here, the generated rpcauth will be inserted into bitcoin.conf

```
Are these values correct?
Is this correct? (y/n):
```

Confirm correct as appropriate.

## Configuring DATUM

```
Where do you want to store datum_gateway_config.json?
Enter path (default: /home/bitcoin/datum):
```

.

`Enter bitcoind rpcurl (default: localhost:28332): `

The default suggested here is what Bitcoin Knots will be listening on. If you don't use the default here, you must change what's in bitcoin.conf

`Enter bitcoind rpcuser (default: datumuser): `

This will automatically use what was generated earlier for RPC authentication.

`Enter bitcoind rpcpassword (default: n26yiQyIl1qdbnz89jN3iP5DKhoWystOMXPV-cW-fi8): `

As above

`Enter work_update_seconds (default: 40): `

How often should your DATUM gateway generate new work for connected miners? (New work will generated immediately upon new network blocks regardless of this value).

`Enter stratum listen_port (default: 23334): `

The port your DATUM gateway will listen on. This will need to be inserted into miners you wish to connect to your gateway, along with the IP address.

`Enter max_clients_per_thread (default: 2000): `
`Enter max_threads (default: 10): `
`Enter max_clients (default: 20000): `

These three settings control how you would like to distribute CPU load. This starts to become relevant when running thousands of miners as you want to ensure you are making efficient use of your machine. Having all 2000 of your miners on one thread when you have multiple threads available to you - for example - would not be a great idea.

`Enter vardiff_min (default: 16384): `

This sets the target difficulty at or above which your miners will submit shares.

**Note: Must be a power of 2**
**Note: Will be overridden by OCEAN's minimum difficulty if using OCEAN to split rewards AND OCEAN's mining difficulty is HIGHER than what you set here.**

`Enter pool_address (default: ): `

**This is the Bitcoin address any rewards from lotto-mined blocks will go to!**

If mining on OCEAN, this address will only be used if `pool_pass_full_user` is set to `false`. If this is set to `true` then this Bitcoin address won't be used for anything OCEAN related - though may be used in certain failure modes for lotto mining depending on how you configure your gateway. (More on that further down).

**Note: DATUM will not start without a valid Bitcoin address entered here for safety - however with default settings, OCEAN rewards will go to Bitcoin addresses in your *MINERS*, not what is written here.**

`Enter coinbase_tag_primary (default: DATUM): `

Use this to name your Blocks in lotto-mode. When splitting rewards on OCEAN, whatever you write here will be overwritten with "OCEAN".

`Enter coinbase_tag_secondary (default: ): `

Name your OCEAN blocks! 

![elecktron-block](/images/elektron-block.png)

`Enter API listen_port (default: 7152): `

The port for your dashboard - this is the graphical interface for DATUM that will become available once this script is complete and your Bitcoin node is synced. (DATUM will not be able to start up until the node is synced).

**To access the dashboard head to the IP of the machine followed by :7152 or whatever port you set here.**

`Log to file? (true/false) (default: true): `
`Enter log file path (default: /home/bitcoin/datum/logs/datum.log): `
`Enter log level (0-3) (default: 0): `

DATUM is still in beta so all logging is appreciated in case bugs are found! (Log level 0 grabs the most possible, 3 the least).

`Enter pool host (default: datum-beta1.mine.ocean.xyz): `

By default you will mine on OCEAN and split rewards with other OCEAN miners. If you do not wish to do this, remove what is written here. (Just put one space before pressing return).

You can also replace this with any other DATUM supporting pool. At the time of writing, only OCEAN supports DATUM.

`Enter pool port (default: 28915): `

This is the port offered by OCEAN for miners using DATUM. It does not need to be removed if not wishing to split rewards on OCEAN and can just be left in all scenarios.

`Pass workers to pool? (true/false) (default: true): `

If you would like stats on [OCEAN](https://ocean.xyz/dashboard/) on a per-worker basis, set this to `true`. If not, change to `false`.

`Pass stratum miner usernames as raw usernames to the pool? (true/false) (default: true): `

Leave this as `true` for rewards on OCEAN to go to the Bitcoin address set in your **miners**. 

If wishing for rewards to go to the address you added for `pool_address` above, then set this to `false`. 

If lotto-mining then true/false makes no difference here.

`Pooled mining only? (true/false) (default: true): `

Set this to true if you would like your miners to fail over to pool 2 should OCEAN go offline.

Set this to false if you would like your miners to fail over to lotto mining should OCEAN go offline.

`Is this correct? (y/n):`

Confirm both configurations. (They can be changed if required.)

```
File 'bitcoin_knots.service' has been created and user inserted correctly.
Do you want to enable and start the service now? (y/n):
```

This will run Bitcoin Knots as a systemd service that will automatically run on startup - this means if your machine gets turned off, everything will JustWork™ upon restart without requiring manual intervention (assuming the device is set to automatically power on once power returns.)

```
File 'datum.service' has been created and user inserted correctly.
Do you want to enable and start the service now? (y/n):
```

This will do the above for the DATUM gateway service ensuring power cycles do not require manual intervention.

**Script complete!**

## Monitoring Sync Progress

Your Bitcoin node will now sync - you can check it's progress with the following:

`tail -f /home/bitcoin/.bitcoin/data/debug.log`

![its-syncing](/images/its-syncing.png

Stop watching with `ctrl C`

If you want to monitor your DATUM gateway, you can do so with `journalctl -u datum.service -f`

![datum-panic](/images/datum-panic.png)

Stop watching with `ctrl C`

**Note: Your DATUM gateway will keep restarting itself until Knots is full synced. This can take days on cheaper harder, and typically takes 12 or so hours on more powerful hardware.**

Once synced, DATUM will startup and you will see DATUM sending work to 0 stratum clients:

![0-clients](/images/0-clients.png)

## Mining

Find the IP of your machine with `ip a` and look for `196.168.....` or `10.0.....` or less often `172......`

![ipa](/images/ipa.png)

Go to your miner's interface and enter the following for Pool URL/Pool Host or similar:

![pool-host](/images/pool-host.png)

(Your miner may not require/allow adding `stratum+tcp://` and/or may or may not want the port in a separate box.)

Add your Bitcoin address:

![bitcoin-address](/images/bitcoin-address.png)

Append a worker name if desired:

![bitcoin-address-worker](/images/bitcoin-address-worker.png)

## Checking the Dashboard

Head to a browser on the same network as your DATUM machine and enter the IP address of the DATUM machine and add the port set under `API` in DATUM's config:

`192.168.x.y:7152`

![dashboard](/images/dashboard.png)

Here you can monitor connected miners and change settings. (Instructions coming for how to enable the interactive dashboard).

## Modifying the Config files

If you wish to change settings in either Bitcoin Knots or the DATUM gateway:

`nano /home/bitcoin/.bitcoin/bitcoin.conf` and `nano /home/bitcoin/datum/datum_gateway_config.json` respectively.

Once inside, you can modify settings as needed and save & quit by pressing `ctrl X` then `y` then `enter`.

You will then need to restart the relevant service whose config you just changed with:

`systemctl restart bitcoin_knots.service` and/or `systemctl restart datum.service`
