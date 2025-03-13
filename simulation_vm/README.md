### Installation
For installation, first keep the VM on a single NAT adapter in VirtualBox to make it easier to download everything. Note that you will change this later.

#### Simulation
1. Install jsoncpp, lapacke, pymodbus: `sudo apt-get install libjsoncpp-dev liblapacke-dev python-pymodbus`
2. Install gcc and make: `sudo apt-get install build-essential`
3. Clone the repo into the home directroy `git clone https://github.com/Fortiphyd/GRFICSv2`
4. Go to the simulation folder: `cd ~/GRFICSv2/simulation_vm/simulation`
5. Compile: `sudo make`

#### Web Visualization
1. Install php and apache2: `sudo apt install apache2 php libapache2-mod-php`
2. Remove existing index.html: `sudo rm /var/www/html/index.html`
3. Create a symlink to the apache folder `sudo ln -s ~/GRFICSv2/simulation_vm/web_visualization/* /var/www/html`

#### Networking
If you have not already, add 2 host-only adapters in VirtualBox:

VirtualBox Host-Only Ethernet Adapter #2: 192.168.95.111 and 255.255.255.0 netmask
VirtualBox Host-Only Ethernet Adapter #3: 192.168.90.111 and 255.255.255.0 netmask

The simulation VM needs to be assigned the ip addresses 192.168.95.10-15 on the 192.168.95.111 host-only network. Power the VM down, and change the NAT adapter to the host-only adapter for the 95.111 network. 

1. Edit the interface file: `sudo nano /etc/network/interfaces` to this:
 ```
auto lo
iface lo inet loopback

auto [NIC]:0
iface [NIC]:0 inet static
address 192.168.95.10
netmask 255.255.255.0

auto [NIC]:1
iface [NIC]:1 inet static
address 192.168.95.11
netmask 255.255.255.0

auto [NIC]:2
iface [NIC]:2 inet static
address 192.168.95.12
netmask 255.255.255.0

auto [NIC]:3
iface [NIC]:3 inet static
address 192.168.95.13
netmask 255.255.255.0

auto [NIC]:4
iface [NIC]:4 inet static
address 192.168.95.14
netmask 255.255.255.0

auto [NIC]:5
iface [NIC]:5 inet static
address 192.168.95.15
netmask 255.255.255.0
```
where [NIC] is the name of your network, most likely enp0s3

2. Restart networking: `sudo systemctl restart networking`

### Python Script boot execution
In the original repository, there was a Bash script `run_all.sh` calling several Python scripts that's running at boot, that brings within minutes of VM startup the internal plant into destruction. To find it i've used the following command in order to shows all available drives on the system, regardless of whether they are active or not:
`systemctl list-unit-files`
![image](https://github.com/user-attachments/assets/2b271bd7-9379-4c89-8abf-9d6814add3b9)
Reading the unit definition with `systemctl cat grfics.service` we see the script:
![image](https://github.com/user-attachments/assets/1f5a98e8-bab6-43be-9593-830aa8e80eff)
I've modified the Unit in order to execute only the binary file that start Unity WebGL simulation:
![image](https://github.com/user-attachments/assets/717b5bff-fda5-4bbc-9b0b-f4616bb4a5fd)
![image](https://github.com/user-attachments/assets/b5d00896-3daf-47df-9241-abe297d23952)

