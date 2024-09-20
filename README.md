# Hping3-Attack-and-Analysis-of-Pcap



Description


Red Side Attack - SYN Flood: In the second deliverable of this cybersecurity project, I executed a simulated TCP SYN Flood attack using Kali Linux and hping3 on my own computer. The objective was to demonstrate the ease with which attackers can inundate a system with a barrage of malicious TCP packets, causing a surge in CPU usage and initiating a successful attack. Blue Team Analysis - Incident Response:
Languages and Utilities Used
Linux
Wireshark
Virtual Box
Environments Used
Windows 10 (21H2)



Project walk-through:

Launching the Attack (Step 1):

<img width="613" alt="Screenshot 2024-09-20 at 1 06 03 PM" src="https://github.com/user-attachments/assets/80e104c3-8df8-4a07-9fb6-e6cac0e95236">



Launching the Attack (Step 2)





<img width="541" alt="Screenshot 2024-09-20 at 1 09 27 PM" src="https://github.com/user-attachments/assets/55f07a9b-81a1-4390-8c74-726aa44318af">
<img width="570" alt="Screenshot 2024-09-20 at 1 09 52 PM" src="https://github.com/user-attachments/assets/cc7b5da9-25fc-4d51-8226-d3c9d54ace82">

<img width="576" alt="Screenshot 2024-09-20 at 1 11 55 PM" src="https://github.com/user-attachments/assets/f20c5717-9f5b-4033-a2e2-e7df1df0b50a">






Executing command for the attack:






<img width="567" alt="Screenshot 2024-09-20 at 1 12 53 PM" src="https://github.com/user-attachments/assets/d9be1e9d-16c5-4b58-9bb2-993e48682f6a">

<img width="625" alt="Screenshot 2024-09-20 at 1 13 27 PM" src="https://github.com/user-attachments/assets/22351cdf-a391-412b-a288-f5d9a833cc83">


Increase in CPU utilization:

<img width="668" alt="Screenshot 2024-09-20 at 1 14 23 PM" src="https://github.com/user-attachments/assets/6d709d54-4f7f-4aa1-a2d2-f0bb0479da1f">





Steps required to complete the attack
Using the command prompt and typing “ipconfig” I made a note of my IP Address.
Opened Kali Linux using Virtual Box. First, we must install hping3 using the “sudo-apt get install hping3”. Refer to the screenshot below.
3. Next, I used the following command. hping3 -c 15000 -d 120 -S -w 64 -p 80 --flood --rand-source 10.178.56.229. There might be an error of “can't open raw socket”. For this, we must first use “sudo -s” which is used to gain elevated privilege. Refer to the screenshot below.
4. Let's break down the above command. -c 15000 means we are sending 15000 packets. -d 120 means each packet is 120 bytes. -S says SYN flag is enabled with a TCP window size of 64(-w 64), we are directing the attack on port 80 with -p 80. –flood indicates sending packets as fast as possible. --rand source helps with spoofed IP addresses to disguise the real source.
Blue Team Analysis
Description
In response to the SYN Flood attack, the Blue Team conducted an incident analysis on a suspicious PCAP file received on November 10, 2023. The analysis was led by Hammaz Ahmed, the Incident Response Analyst.
Project walk-through:



<img width="664" alt="Screenshot 2024-09-20 at 1 15 08 PM" src="https://github.com/user-attachments/assets/36f97ef1-b7aa-44a1-a7b8-014f44edded6">

<img width="662" alt="Screenshot 2024-09-20 at 1 15 29 PM" src="https://github.com/user-attachments/assets/fd3ddb09-1a0f-4acd-aa9e-1e3f10ca917f">


<img width="662" alt="Screenshot 2024-09-20 at 1 15 46 PM" src="https://github.com/user-attachments/assets/ee9409b4-0186-4f04-b6ae-c97acb42ca98">





Step-by-Step Analysis
First, I took a scroll glance at the whole pcap file. It had some red flags for the TCP stream. Hence, I filtered by typing “tcp” in the filter column. Not much can be drawn from just this.
I used the filter “tcp.flags.syn==1 && tcp.flags.ack==0”. This filter helps in TCP packets that are part of the 3-way handshake. Missing a proper 3-way handshake can give us a hint of synflood attacks. In fact, we do see loads of synchronization packets but no sign of a complete handshake.



I can also see a sudden rise in packets and continuously receiving similar ones. This is also an indicator of a synflood attack.
If we look at the time interval in which the packets are coming, it is suspicious since the time gap is very low. That is an extremely large number of requests occurring in a brief interval of time.
I can also see an increase in “TCP Spurious Transmission”. The receiver is receiving a retransmitted segment even before the ACK packet is sent. This can be an indicator of a synflood attack. The below screenshot also highlights “TCP dup ACK” which shows the arrival of multiple ACK packets. This is usually due to network congestion, or packet loss (another indicator of a SYN Flood Attack)



Conclusion



Sudden Increase in SYN Packets.
Incomplete 3-way Handshakes.
TCP Spurious Transmissions and TCP dup ACK.
Large number of similar traffic within a small time frame.
After critically investigating the pcap file it is clear that there has been a SYN Flood Attack (Discussed above) since there are several indicators. Noticing the severity and unambiguousness of this event, it will be reassigned to the Incident Response Team Manager. The incident was analyzed but needs further investigation and clarification to point out the severity and if any denial of service happened. A follow-up report will be generated for this event.



Recommendations



This type of attack overwhelms the network by sending tons of connection requests, potentially leading to disruption of service. While our team is actively monitoring the situation, I recommend some countermeasures. • Firewalls: Configure your firewall to detect and block malicious SYN flood traffic. This may involve setting up rules to block traffic from specific source IP addresses or implementing heuristics to identify abnormal traffic patterns. • TCP Timeout Adjustment: Adjust the TCP timeout values on your server. By tweaking these values, you can potentially reduce the impact of SYN flood attacks by releasing half-open connections more quickly. • Syn Cookies: Enable SYN cookies on our server. SYN cookies are a technique that allows the server to validate connection requests without maintaining a full connection state until the three-way handshake is complete. This can help mitigate the impact of a SYN flood attack. • Improve Network Monitoring: Strengthen our network monitoring capabilities to promptly detect and respond to any unusual patterns or irregularities in our traffic. • Review Security Policies: Regularly review and update our security policies to ensure they align with the latest best practices and are effective against evolving threats. • Utilize load balancers to distribute incoming traffic across multiple servers. This can help distribute the impact of a SYN flood attack, making it more difficult for the attacker to overwhelm a single server.



