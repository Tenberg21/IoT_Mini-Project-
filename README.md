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
IOT testbed shell commands are executed based on the provided cheat sheet.
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
sudo apt-get install

Using the COAP-server script, I tried to work around my challenges with FIT IoT-LAB and RIOT-OS. This resulted in a system where the server actively polls the node for data, rather than the other way around.
The implementation could probably be easily improved. For example, after the node registers with the server, it could wait for the server to specify the polling frequency it wants from the node. Then, the node could be put to sleep during most of the time and only wake up to send the data.

#### SQLite
sudo apt-get install
#### Grafana
sudo apt-get install
