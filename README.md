## Football Coin and Multichain integration

Install Multichaind service, run full node:
https://www.multichain.com/download-community/

To connect to the FootballCoin blockchain using the Multichain client you need to run this command:  
``multichaind FBCCHAIN@wallet.xfccoin.io:7269 -txindex=1 -daemon &``

Then to setup ElectrumX server itself:
```
sudo apt install python3-pip
git clone https://github.com/llilakoblock/electrumx.git
cd electrumx
pip3 install .
```

Use this guide to setup Systemd service:

https://electrumx-spesmilo.readthedocs.io/en/latest/HOWTO.html#using-systemd  
**Do not forget to change user in Systemd unit file and path to executable (defaulted to electrumx user, and /usr/local/bin)!**  
Should be something like this, as electrumx is installed in /home/user/.local/bin by default:  
```
[Unit]
Description=Electrumx
After=network.target

[Service]
EnvironmentFile=/etc/electrumx.conf
ExecStart=/home/ivan/.local/bin/electrumx_server
ExecStop=/home/ivan/.local/bin/electrumx_rpc -p 8000 stop
User=ivan
LimitNOFILE=8192
TimeoutStopSec=30min

[Install]
WantedBy=multi-user.target
```

Before starting ElectrumX service, you have to create conf file:  
``/etc/electrumx.conf``  
Create db folder:  
``mkdir ~/db``  

Add this info to electrumx.conf:

```
DB_DIRECTORY = /home/youruser/db
DAEMON_URL = http://rpcuser:rpcpassword@127.0.0.1:7268/
COIN = FootballCoin
NET = mainnet
SERVICES = tcp://:50001,rpc://:8000
```

Where _youruser_ is linux username, _rpcuser_ and _rpcpassword_ credentials from file:  
``~/.multichain/FBCCHAIN/multichain.conf``  
Which will be generated after first start of multichaind daemon.

Then just start ElectrumX service via systemctl:  
``sudo systemctl status electrumx.service``

ElectrumX server will start to respond after multichaind daemon full sync **plus** electrumx service full sync.
