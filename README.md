# Custom-Snort-Detection Rules

## Objective
In this PCAP, the SOC team suspects that an adversary may have gained unauthorized access to on of their web servers by exploiting a Local File Inclusion (LFI). Also, multiple DLP alerts were activated, raising concerns that sensitive files may have been extracted. This challenge wants me to create custom detection rules for each different scenarios. 

There are no walkthrough videos or vlogs to verify if I'm doing it correctly—just a list of questions that I have to solve on my own.

### Skills Learned

- Configure and manage Snort to detect cyber alerts and threats
- Write custom basic Snort rules to enhance threat detection
- Learn fundamental knowledge of automating threat detection and response by writing custom detection rules and optimizing alerts

### Tools Used

- Snort
- Wireshark
- Snorpy 

## RULE 1
The web server's login page returns an HTTP 401 status code when there is a failed login attempt. Write a rule to detect where a single IP address generates 10 failed loging attempts within a 30 second window.

![Screenshot 2025-02-21 165436](https://github.com/user-attachments/assets/3c5a138e-7e07-4a01-9d40-cd2ab1bdba56)
![Screenshot 2025-02-21 192409](https://github.com/user-attachments/assets/acfc83d4-4ae4-4437-9bbf-59fd3ccc6634)

- First, I used Wireshark to identify where the HTTP 401 status code appeared in the traffic
- (sourceIP) (sourcePORT) = any 80 (I'm looking at the HTTP response header so the source port should be 80)
- http_stat_code (want to tell Snort to look at the HTTP STAT CODE to find the 401 status code)

![Screenshot 2025-02-21 165237](https://github.com/user-attachments/assets/8441d25b-2688-4bd3-bb3f-f72f2b01450d)
![Screenshot 2025-02-21 165245](https://github.com/user-attachments/assets/bb179292-bdb3-42ca-a4a9-80e376fc8cb6)
![Screenshot 2025-02-21 165303](https://github.com/user-attachments/assets/8c0b672f-f9b9-4053-92c6-e066b50ef336)
- Alert Worked! Total of 174 packets detected!

## RULE 2
When a user successfully logs in, an HTTP 302 status code directs them to the admin portal. Write a Snort rule to detect these successful login attempts.

![Screenshot 2025-02-21 194034](https://github.com/user-attachments/assets/dbe8caa8-a222-40e4-a3b4-0058ee9ffb0a)
![Screenshot 2025-02-21 165728](https://github.com/user-attachments/assets/904266f4-b25a-4bba-8851-7dd9c14574a5)
- 2 Successful Logins Detected!

## RULE 3
Typical Local File Inclusion (LFI) attack involve using the ../ sequence within URI parameters to navigate through directories and access restricted files outside the web root. Write a Snort rule to detect any packets containing this string in the HTTP URI.

![Screenshot 2025-02-21 195428](https://github.com/user-attachments/assets/0aa57cdf-699d-45fd-8c0c-dfd1b663bd84)
![Screenshot 2025-02-21 195343](https://github.com/user-attachments/assets/ce021c60-207d-4af3-8a70-a438c317b53c)
![Screenshot 2025-02-21 195528](https://github.com/user-attachments/assets/990a3d46-2c1b-4de3-978d-321396e8bc1b)

- First, I wanted to see "../" looks like in HEX. (searched through Wireshark)
- Got the HEX code from the translator on the bottom right section of Wireshark

![Screenshot 2025-02-21 194116](https://github.com/user-attachments/assets/f490e9f1-873c-4156-af4d-0b9b782b7e44)
![Screenshot 2025-02-21 171313](https://github.com/user-attachments/assets/4b022208-a4ab-44f1-b7dc-0b03dec7abd7)
- Tell Snort to look for |2E 2E 2F| HEXcode in HTTP_URI section of the packet
- 5 LFI Attempts Detected!

## RULE 4 
By exploiting LFI, the adversary extracted a private SSH key from the web server. Using the file signature of OpenSSH private key, write a Snort rule to detect any occurences of these private keys being transmitted over the network.

![Screenshot 2025-02-21 171552](https://github.com/user-attachments/assets/5e10df61-7797-4854-99e8-1c726946f95b)
![Screenshot 2025-02-21 194142](https://github.com/user-attachments/assets/53c0e394-2efc-4a73-b418-52cf182d74a2)
![Screenshot 2025-02-21 194203](https://github.com/user-attachments/assets/65ae12bf-c246-41ef-b5ca-4a3defe61bbb)

- First, I found the hex code used for OpenSSH private key file signatures
- (sourceIP) (sourcePORT) = any 80 (I want to see data being stolen from OUR web server)
- Tell Snort to look for matching hex code of the FILE_DATA
- depth:35 (Tell Snort to look at 35 bytes of data - that is how long the hex code is)









