### This guide will explain the various options presented to you in [this](https://github.com/Com320/OC-mech-datum-boxes) script which will be run during the installation methods presented [here](https://github.com/bitcoinmechanic/datum-guides).

**Any of these settings can be changed after completing this script. Some basic configuration files can be trivially changed (i.e the secondary coinbase text in your DATUM gateway config used to label your OCEAN blocks) - others will be more fiddly, like the Debian user running your Bitcoin node and DATUM gateway.**

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

```
Enter location for bitcoin.conf (default: /home/bitcoin/.bitcoin/bitcoin.conf): 
```

This file is the configuration file for the Bitcoin Knots node. Any changes you want to make to the way your node runs will likely require manually editing the file and restarting the node.

```
Enter location for data (default: /home/bitcoin/.bitcoin/data): 
```

This is where the blockchain data will live along with a few other files.

```
Enter value for 'prune' (default: 550): 
```

By default, we suggest pruning, however it is fine to enter `0` here if you do not wish to prune. DATUM does not require an archival node.

```
Enter value for 'dbcache' (default: 100): 
```

This higher this value the faster your node will sync. (Unit = MB).

```
Enter value for 'rpcauth' (default: datumuser:31368a1605d56dde8882fc6bc785037a$a5fd79c67a23d3db18128f7c675acb684bb05dffce479fa1ebc4a916834f4163):
```

Here, the generated rpcauth will be inserted into bitcoin.conf

```
Are these values correct?
Is this correct? (y/n):
```

Confirm correct as appropriate.

```
Where do you want to store datum_gateway_config.json?
Enter path (default: /home/bitcoin/datum):
```

.

```
Enter bitcoind rpcurl (default: localhost:28332): 
```

The default suggested here is what Bitcoin Knots will be listening on. If you don't use the default here, you must change what's in bitcoin.conf

`Enter bitcoind rpcuser (default: datumuser): `

This will automatically use what was generated earlier for RPC.
