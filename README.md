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

## IOT
#### Node
#### Border Router
_____________________
## AWS
#### COAP Server
#### SQLite
#### Grafana
