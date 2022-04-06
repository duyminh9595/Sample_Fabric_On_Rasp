# Sample_Fabric_On_Rasp
cd /home/pi/Sample_Fabric_On_Rasp/test-network/docker
docker-compose -f docker-compose-ca.yaml down
docker-compose -f docker-compose-ca.yaml up -d


source ./organizations/fabric-ca/registerEnroll.sh
# create ca orderer
createOrderer 
# create ca org1 peer
createOrg1

# create genesis
cd /home/pi/Sample_Fabric_On_Rasp/test-network
./scripts/createGenesis.sh 

# create anchor peer tx
./scripts/createChannelTx.sh 

# create peer,orderer
cd /home/pi/Sample_Fabric_On_Rasp/test-network/docker
docker-compose -f docker-compose-test-net.yaml up -d

# create cli
docker-compose -f docker-compose-cli.yaml up -d

# inside cli
docker exec -it <container id> bash

export CHANNEL_NAME=mychannel 
<!--  -->
echo $CHANNEL_NAME
<!-- fetch  -->
./scripts/create_app_channel.sh
<!-- join channel -->
peer channel join -b ./channel-artifacts/mychannel.block
<!-- update anchor peer -->
./scripts/updateAnchorPeer.sh mychannel Org1MSP
export CC_NAME=basic 
./scripts/package_cc.sh 
./scripts/install_cc.sh

<!-- set the tag baseos -->
docker tags ottoflaherty/fabric-baseos:arm64-2.3 hyperledger/fabric-baseos:latest
<!-- set the tag ccenv -->
docker tag ottoflaherty/fabric-ccenv:arm64-2.3 hyperledger/fabric-ccenv:latest
export CHANNEL_NAME=mychannel 

export CC_NAME=basic 
export PACKAGE_ID=basic_1:9820659c595e662a849033ca23b4424e87a126e8f40b5f81ace59820b81fe8e7
./scripts/approve_cc.sh 

./scripts/commit_cc.sh 

./scripts/invoke_cc.sh 

