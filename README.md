'-cd $HOME
git clone https://github.com/strangelove-ventures/half-life
cd half-life
wget -cO - https://raw.githubusercontent.com/planq-network/half-life/main/config.yaml.example > config.yaml
MONIKER=$(mantrachaind status | jq -r .NodeInfo.moniker)
RPCADDRESS=$(mantrachaind status | jq -r .NodeInfo.other.rpc_address)
CONSENSUSADDRESS=$(mantrachaind tendermint show-address)
sed -i "s/DISCORD_WEBHOOK_ID/1072203008106041457/" $HOME/half-life/config.yaml
sed -i "s/DISCORD_WEBHOOK_TOKEN/1HnyTKIFWu3Y07XzYxWS2xcw_zRphjQ5ZntCSfiIYwxI0SMQtLngXiKJKGSXjGIRngRF/" $HOME/half-life/config.yaml
sed -i "s/monikername/$MONIKER/" $HOME/half-life/config.yaml
sed -i "s#tcp://localhost:26657#$RPCADDRESS#" $HOME/half-life/config.yaml
sed -i "s/mantravalcons1/$CONSENSUSADDRESS/" $HOME/half-life/config.yaml
go install'

Every discord account has a unique Discord ID, which cannot be altered. 
To get your unique Discord ID, first you need to go to settings (cog in bottom left) > Advanced > and toggle on developer mode. Right click on your own user, and click "Copy ID "
Replace DISCORD_USER_ID in $HOME/half-life/config.yaml with your own Discord ID (leave the brackets).

sudo tee /etc/systemd/system/halflife.service << EOF
[Unit]
Description=Halflife
After=network.target
[Service]
Type=simple
Restart=always
RestartSec=5
TimeoutSec=180
User=$(whoami)
WorkingDirectory=$HOME/half-life
ExecStart=$(which halflife) monitor
LimitNOFILE=infinity
NoNewPrivileges=true
ProtectSystem=strict
RestrictSUIDSGID=true
LockPersonality=true
PrivateUsers=true
PrivateDevices=true
PrivateTmp=true
[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl enable halflife
systemctl start halflife

journalctl -u halflife -f
