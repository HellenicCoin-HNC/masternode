How do I setup a masternode? (HNC) 

Use the following instructions to setup a masternode for a X11 based coin on Ubuntu Server 18.04. 

Make sure that you have the following requirements. 

- Required amount of coins to setup the masternode.  
- A wallet to store your coins.  
- A server or VPS. 

 

Install the required dependencies 
```
  wget https://raw.githubusercontent.com/HellenicCoin-HNC/node/main/install-dependencies.sh
  chmod +x install-dependencies.sh
  ./install-dependencies.sh
```

Upload the daemon and tools  
```
wget "https://github.com/HellenicCoin-HNC/node/raw/main/helleniccoin-daemon-linux.tar.gz" -O helleniccoin-daemon-linux.tar.gz 
wget "https://github.com/HellenicCoin-HNC/node/raw/main/helleniccoin-qt-linux.tar.gz" -O helleniccoin-qt-linux.tar.gz 
tar -xzvf helleniccoin-daemon-linux.tar.gz 
tar -xzvf helleniccoin-qt-linux.tar.gz 
sudo mv helleniccoind helleniccoin-cli helleniccoin-tx /usr/bin/ 
```

Create the config file. 
```
 mkdir $HOME/.helleniccoin 
 nano $HOME/.helleniccoin/helleniccoin.conf 
```
Paste the following lines in helleniccoin.conf. 
```
rpcuser=rpc_helleniccoin 
rpcpassword=kuw05sqio7bcm8z96o7redv17xws1lw6xpd1qf33 
rpcallowip=127.0.0.1 

listen=1 
server=1 
daemon=1 
maxconnections=64 

#masternode=1 
#masternodeblsprivkey= 
externalip=REPLACE_WITH_EXTERNAL_IP_OF_VPS 
```

Leave the fields “masternode” and “masternodeblsprivkey” commented out. 

Replace the text “REPLACE_WITH_EXTERNAL_IP_OF_VPS” with the external IP address of your VPS.  
 
E.G. externalip=136.144.171.201 

Start your node with the following command. 

helleniccoind 

Wait until the command “mnsync status” returns the status “MASTERNODE_SYNC_FINISHED”. 
 

helleniccoin-cli mnsync status 

Example output 
```
{ 
  "AssetID": 999, 
  "AssetName": "MASTERNODE_SYNC_FINISHED", 
  "AssetStartTime": 1558596597, 
  "Attempt": 0, 
  "IsBlockchainSynced": true, 
  "IsSynced": true, 
  "IsFailed": false 
} 
```

Send the collateral 

Create a new address for the fee. 

helleniccoin-cli getnewaddress 

Example output 
 `THBSKbJgn7g2x3PAbNuW6whkecqrq9aUn6`

Send 1 coin to the address that you just created. 

Create a new address for the masternode collateral. 

helleniccoin-cli getnewaddress 

Example output `TRPLV4dXmEFMSgXg2Xu6skN9pmw8TAo4N5`

Transfer the required amount of coins (1000000) to the address that you just created.  
Wait until the transaction has the required masternode confirmations.(19) 

Identify the funding transaction. 

helleniccoin-cli masternode outputs 

Example output 
```
{ 
  "fdab9dff1ff9caf5d291905ad43b9f7d69775189d4d22cb085d7fedd94ea1c6a": "0" 
} 
```
 

Register your masternode 

Generate a BLS key pair. 

helleniccoin-cli bls generate 

Example output 
```
bls generate 
{ 
  "secret": "0acbf6f183d0c9b794b9bc0dba25f8a1a1eca21aa4f2e4a86ecd3120a59efb35", 
  "public": "064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9" 
} 
```
 
Place the secret key in the config file of your masternode and uncomment (delete #) the values “masternode” and “masternodeblsprivkey”. 
Example config 
```
#---- 
rpcuser=rpc_helleniccoin 
rpcpassword=kuw05sqio7bcm8z96o7redv17xws1lw6xpd1qf33 
rpcallowip=127.0.0.1 
#---- 
listen=1 
server=1 
daemon=1 
maxconnections=64 
#---- 
masternode=1 
masternodeblsprivkey=0acbf6f183d0c9b794b9bc0dba25f8a1a1eca21aa4f2e4a86ecd3120a59efb35 
externalip=136.144.171.201 
#---- 
```

Restart your masternode using the following commands. 

helleniccoin-cli stop 
helleniccoind 
 
Prepare a ProRegTx transaction 
Create a new address for the owner. 
helleniccoin-cli getnewaddress 

Example output 

TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN 

Create a new address for the voting. 

helleniccoin-cli getnewaddress 

Example output 

TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL 

Create a new address for the payout. 

getnewaddress 

Example output 

TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD 

Modify the following line. (using notepad) 

Helleniccoin-cli protx register_prepare fdab9dff1ff9caf5d291905ad43b9f7d69775189d4d22cb085d7fedd94ea1c6a 0 136.144.171.201:9999 TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN 064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9 TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL 0 TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD THBSKbJgn7g2x3PAbNuW6whkecqrq9aUn6  

fdab9dff1ff9caf5d291905ad43b9f7d69775189d4d22cb085d7fedd94ea1c6a - Transaction id from the command “masternode outputs”. 

0 - Single digit from the command “masternode outputs”. 

136.144.171.201:31052 - External IP address and P2P 31052of your Masternode. #IP#:31052 

TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN - The new address generated above for the owner. 

064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9 - The BLS public key generated above. 

TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL - The new address generated above for the voting. 

0 - Leave this value at 0. 

TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD - The new address generated above for the payment. 

THBSKbJgn7g2x3PAbNuW6whkecqrq9aUn6 - The new address generated above for the fee. 

Paste the modified line into the console of your wallet. 

Example output 

 

{ 

  "tx": "0300010001a55ee8d6ad1d5c1409a5328f4e53e80e3e7c83cf85253594141505fa64c5eeec0000000000feffffff0121dff505000000001976a9144f18fd993c0f9458fafb4985536dd358e9899a9f88ac00000000d10100000000006a1cea94ddfed785b02cd2d4895177697d9f3bd45a9091d2f5caf91fff9dabfd0000000000000000000000000000ffff88909c3714c4e172bd9c230db9cac3e446945cff2ea6720c2eca064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9b9772f1f7b0af05a67883806a4f60ddde4ecbf9e00001976a9143206f92dd6acc1a481cbb88fcadc19d0507bcb7d88ac0c5537044361500975adba10f9299f684f62a7f544e8e671638fee2c3914349f00", 

  "collateralAddress": "TRPLV4dXmEFMSgXg2Xu6skN9pmw8TAo4N5", 

  "signMessage": "TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD|0|TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN|TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL|ac19e80b02d4e8a27feb42073114070a281a2b788ba064803e8064d259b22ebc" 

} 

 

Sign the ProRegTx transaction 

Modify the following line(using notepad). 

helleniccoin-cli signmessage "TRPLV4dXmEFMSgXg2Xu6skN9pmw8TAo4N5" "TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD|0|TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN|TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL|ac19e80b02d4e8a27feb42073114070a281a2b788ba064803e8064d259b22ebc"  

TRPLV4dXmEFMSgXg2Xu6skN9pmw8TAo4N5 - The “collateralAddress” value of the above output. 

TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD|0|TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN|TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL|ac19e80b02d4e8a27feb42073114070a281a2b788ba064803e8064d259b22ebc - The “signMessage” value of the above output. 

Paste the modified line into the console of your wallet. 

Example output 

H/d9tkCSzqdYh8qLL1c+KDIlrb4vtFSfdxd88XDc3U/hRZ6lMuAR8TULy7vh1YXGk6AYFFV1xyPNuEdZVMN9SdI= 

 

Submit the signed message 

Modify the following line (using notepad) 

helleniccoin-cli protx register_submit 0300010001a55ee8d6ad1d5c1409a5328f4e53e80e3e7c83cf85253594141505fa64c5eeec0000000000feffffff0121dff505000000001976a9144f18fd993c0f9458fafb4985536dd358e9899a9f88ac00000000d10100000000006a1cea94ddfed785b02cd2d4895177697d9f3bd45a9091d2f5caf91fff9dabfd0000000000000000000000000000ffff88909c3714c4e172bd9c230db9cac3e446945cff2ea6720c2eca064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9b9772f1f7b0af05a67883806a4f60ddde4ecbf9e00001976a9143206f92dd6acc1a481cbb88fcadc19d0507bcb7d88ac0c5537044361500975adba10f9299f684f62a7f544e8e671638fee2c3914349f00 H/d9tkCSzqdYh8qLL1c+KDIlrb4vtFSfdxd88XDc3U/hRZ6lMuAR8TULy7vh1YXGk6AYFFV1xyPNuEdZVMN9SdI=  

0300010001a55ee8d6ad1d5c1409a5328f4e53e80e3e7c83cf85253594141505fa64c5eeec0000000000feffffff0121dff505000000001976a9144f18fd993c0f9458fafb4985536dd358e9899a9f88ac00000000d10100000000006a1cea94ddfed785b02cd2d4895177697d9f3bd45a9091d2f5caf91fff9dabfd0000000000000000000000000000ffff88909c3714c4e172bd9c230db9cac3e446945cff2ea6720c2eca064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9b9772f1f7b0af05a67883806a4f60ddde4ecbf9e00001976a9143206f92dd6acc1a481cbb88fcadc19d0507bcb7d88ac0c5537044361500975adba10f9299f684f62a7f544e8e671638fee2c3914349f00 - The “tx” value of the “protx register_prepare” command. 

H/d9tkCSzqdYh8qLL1c+KDIlrb4vtFSfdxd88XDc3U/hRZ6lMuAR8TULy7vh1YXGk6AYFFV1xyPNuEdZVMN9SdI= - The output of the above command. 
 

Paste the modified line into the console of your wallet. 

Example output 

7da2e1187202a1a497beca05e0e53a6e4df0dc06046f72fbf8b61c942db2982a 
 

Your masternode is now registered and will appear in the masternode list. 

You can check the status of your masternode using the command "masternode status". 
 

helleniccoin-cli masternode status 

Example output 

 

{ 

  "outpoint": "fdab9dff1ff9caf5d291905ad43b9f7d69775189d4d22cb085d7fedd94ea1c6a-0", 

  "service": "136.144.171.201:9999", 

  "proTxHash": "7da2e1187202a1a497beca05e0e53a6e4df0dc06046f72fbf8b61c942db2982a", 

  "collateralHash": "fdab9dff1ff9caf5d291905ad43b9f7d69775189d4d22cb085d7fedd94ea1c6a", 

  "collateralIndex": 0, 

  "dmnState": { 

    "service": "136.144.171.201:9999", 

    "registeredHeight": 145, 

    "lastPaidHeight": 0, 

    "PoSePenalty": 0, 

    "PoSeRevivedHeight": -1, 

    "PoSeBanHeight": -1, 

    "revocationReason": 0, 

    "ownerAddress": "TWXGVYPHGA4gWcZ9Zp2qnubFVNagvorwEN", 

    "votingAddress": "TSsrnbtJGYD1WmVsrsvauuwxibJyqkkqqL", 

    "payoutAddress": "TEXj9AgdCh1giGmr1BXpYsngmkMkthNngD", 

    "pubKeyOperator": "064bb1741f4707cfe3629176857c41e0d23cbe751061fe5d0d67b506db10c8f3f6f2b684c3cec8e4a128193a001d12e9" 

  }, 

  "state": "READY", 

  "status": "Ready" 

} 

 

 
