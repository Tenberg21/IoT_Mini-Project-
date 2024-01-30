# IoT_Mini-Project-
521043S-3004 Internet of Things 2023 IoT_Mini-Project 

# Video demo: 
https://unioulu-my.sharepoint.com/:v:/g/personal/tenberg21_student_oulu_fi/EY80TRzjA71Js9gbGk_jg_wB4AnAYqz61j8T1vGMBA0uAQ?e=sisSUE

Video description:

**Before the Video:**
- The IOT-testbench experiment has commenced with 2 M3 nodes.
- Firmware for both the Client and Border Router has been successfully flashed.
- An AWS server has been initiated, and its IPV6 address has been copied for use in the COAP put command.
- Additionally, the Client's IPV6 address has been obtained through the "ifconfig" shell command and is ready for use in the put command.

**Start of the Video:**
1. The COAP server Python script is launched on the AWS cloud.
2. The Node ID transmits its IPV6 address to the server using the COAP topic "/node_info" through the Border Router.
3. The COAP server receives the Node's IPV6 address and initiates polling on the IPV6 address topic "/temperature."
4. Upon acquiring the temperature value, it is added to the SQLite database, including a timestamp, the Node's IPV6 address, and its ID.
5. Grafana retrieves and visualizes the data from the SQLite database in a time-series diagram.


# Software
The software components include Cloud software, NODE software, and borderrouter software. RIOT-os was utilized for both NODE and borderrouter software, leveraging the example code available at https://github.com/RIOT-OS/RIOT.git.

The cloud server, running on AWS cloud service with an Ubuntu image. The main concept involved the node registering itself to the server, following which the server would initiate requests for data and maintain a database of all registered nodes. The acutall implementation, while basic, serves as a demonstration of the core idea. Although it features minimal functionality, the "pipeline" is complete, ensuring that data travels from the node to the cloud server database and is visually presented.

The database was implemented using sqlite3, and the graphical presentation of data was achieved through Grafana.
## IOT Testbed
IOT testbed was instrumentized with shell commands found on the iot_testlab_cheatsheet.txt as follows:
```bash
//env setup
git clone https://github.com/RIOT-OS/RIOT.git -b 2020.10-branch //Right version for all the documentation & Material



//Experiment

//Client & BorderRouter start experiment
// In directory /RIOT
iotlab-experiment submit -n riot_m3 -d 300 -l 2,archi=m3:at86rf231+site=grenoble    //Start experiment with 2 nodes
iotlab-experiment wait --timeout 30 --cancel-on-timeout                             //Query for ready nodes 
iotlab-experiment --jmespath="items[*].network_address | sort(@)" get --nodes       //Query for node ids

//BorderRouter

source /opt/riot.source 
make ETHOS_BAUDRATE=500000 DEFAULT_CHANNEL=15 BOARD=iotlab-m3 -C examples/gnrc_border_router clean all
iotlab-node --flash examples/gnrc_border_router/bin/iotlab-m3/gnrc_border_router.elf -l grenoble,m3,102 // m3,[Nodeid]
ip -6 route                                 //Check free taps & adrresses
sudo ethos_uhcpd.py m3-102 tap2 2001:660:5307:3140::/64                                                 // m3-[Nodeid]

//Client
// ONE THE SAME: CHANNEL,SITE as router
// IN directory /mini_project_1/code

source /opt/riot.source
make ETHOS_BAUDRATE=500000 DEFAULT_CHANNEL=15 IOTLAB_NODE=m3-95 flash                                  // m3-[Nodeid]
make ETHOS_BAUDRATE=500000 DEFAULT_CHANNEL=15 IOTLAB_NODE=m3-95 -C . term                              // m3-[Nodeid]


//ON client terminal to register node to server

coap put [SERVER IPV6 ADDR] 5432 /node_info [NODE IPV6 ADDR]

NODE IPV6 ADDR is attained with ifconfig command
SERVER IPV6 ADDR can be copied from AWS instance details or terminal wellcome message.

```



#### Node
Node firmware was assembled by combining and adapting elements from the RIOT-OS course material examples. The firmware in this repository is not fully utilized, with a significant portion of its functionality left unused. However, this design choice allows for flexibility and leaves room for improvement, which can be implemented on the server side only if required.
#### Border Router
The border router functionality was implemented using the unmodified RIOT-OS example located at "examples/gnrc_border_router." This example was deemed sufficient for the task.
_____________________
## CLOUD
####  AWS Cloud configuration
An AWS EC2 instance was selected and configured with an Ubuntu Linux image. IPv6 addressing was implemented for the instance, following a guide that, while mostly up-to-date, had some discrepancies with the current version of AWS. The guide used for setting up IPv6 addressing can be found at: https://4sysops.com/archives/assign-an-ipv6-address-to-an-ec2-instance-dual-stack/
Subsequently, inbound and outbound addresses were assigned to facilitate the passage of COAP messages and to make Grafana accessible from outside the machine. The following rules were applied, based on the guide:
Inbound/Outbound Rules:

5300-5500 UDP for ::/0 to allow COAP communication
3000 TCP 0.0.0.0/0 for Grafana access



#### COAP Server
Using the COAP-server script, I tried to work around my challenges with FIT IoT-LAB and RIOT-OS. This resulted in a system where the server actively polls the node for data, rather than the other way around.
The implementation could probably be easily improved. For example, after the node registers with the server, it could wait for the server to specify the polling frequency it wants from the node. Then, the node could be put to sleep during most of the time and only wake up to send the data.

Nessecary non python3 standard libraries for the script:
```bash
pip3 install aiocoap
```
#### SQLite
For storing IoT node values, SQLite3 was utilized, employing the following database model:


| ID | Temperature | Timestamp | IPv6Addr |
|----|-------------|-----------|----------|
|    |             |           |          |

Adjusting R/W access for the database file to be accessible by the ubuntu user and grafana user:

```bash
sudo chown -R grafana:grafana /opt/grafana_shared
sudo chown -R ubuntu:ubuntu /opt/grafana_shared/
sudo chmod 755 /opt/grafana_shared/
sudo chmod -R u+w /opt/grafana_shared/
```
#### Grafana

To install Grafana these commands were used:
```bash
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
sudo wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install -y grafana
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

The default port for Grafana, 3000, was utilized. 

The Grafana web interface was accessible through the server's IPV6 address, followed by ":3000," using a web browser.


A straightforward time series dashboard was employed to retrieve data from the SQLite database, using the following query as the data source:
```SQL
SELECT strftime('%s', Timestamp) AS time, Temperature AS value
FROM temperature_data
WHERE Timestamp >= datetime('now', '-2 hours')
ORDER BY time;
```
