<h1>Nmap and Wireshark Lab</h1>

I took some time to utilize a few different Nmap scans and see how exactly the data is being sent across a network and what it looks like in Wireshark. In this README file you'll find screenshots of what my syntax looked like for the Nmap scan, pictures of the results of the scan, and also screenshots of the Wireshark pcaps. You can download the pcap files yourself if you want to do a deeper analysis of the traffic. 

Note: I used the -F flag in all of my scans to only scan for 100 ports maximum. I also had TCP ports 80, 143, and 995 opened via a Netcat listener. I also opened UDP ports 593 and 1645 via a Netcat listener.

Find me on LinkedIn and feel free to read my article.

<h2>Nmap ping scan</h2>

Used a Nmap ping sweep to determine the computers on my network with a -sn flag. You can see my attacking host is 192.168.12.235 and the target machine was 192.168.12.148. 
<img src="https://user-images.githubusercontent.com/107446796/180568373-77133887-27fb-4822-b2dc-b87ba1f64e48.png">

<h2>-sS</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570057-8903c43e-1995-4fbd-a3a5-843216bb97b8.png">
<img src="https://user-images.githubusercontent.com/107446796/180570496-86ba9360-ac44-47df-9e22-7d38854d93b3.png">

<h2>-sT</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570524-560e81f4-15c5-4e48-8b58-a9db7d8d527e.png">
<img src="https://user-images.githubusercontent.com/107446796/180570533-f4464d30-28a5-4556-ae56-a2d182b1d718.png">

<h2>-sA</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570692-b9db068d-f131-4d38-9da7-eaa2f05ec9b7.png">
<img src="https://user-images.githubusercontent.com/107446796/180570700-c50d5e09-e6ef-4c8b-8ed4-325b69d90715.png">

<h2>-sW</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570779-91208809-0042-46b0-828b-0cda672679d6.png">
<img src="https://user-images.githubusercontent.com/107446796/180570787-2ff595c1-67ed-4085-a74c-a5babd3340f2.png">

<h2>-sM</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570863-7db60e7b-eca2-455c-88fe-5e47f0999bd9.png">
<img src="https://user-images.githubusercontent.com/107446796/180570869-894dd00c-fe53-4ff5-b74b-dd6a337a030b.png">

<h2>-sU</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570916-fe82072b-123d-4ee2-949c-d902f21baead.png">
<img src="https://user-images.githubusercontent.com/107446796/180570921-395a4c36-ab58-4bbd-9d2f-0b22df8b7534.png">

<h2>-sN</h2>
<img src="https://user-images.githubusercontent.com/107446796/180570980-38b39589-0673-4043-9c3a-bb5c59c2bb2a.png">
<img src="https://user-images.githubusercontent.com/107446796/180570993-b2cd3941-92e0-40f7-859f-ba481050d488.png">

<h2>-sF</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571108-d83528a0-9b5d-4702-aa34-8991f75d98d9.png">
<img src="https://user-images.githubusercontent.com/107446796/180571116-30086a87-88e7-4d1e-b738-51bed552022e.png">

<h2>-sX</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571189-a5d5a266-5c11-4b53-ac98-4698bb2c8f17.png">
<img src="https://user-images.githubusercontent.com/107446796/180571196-1c1e24e5-7c4c-4329-a51c-a7d7a3b842e7.png">

<h2>-sY</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571517-1abb92ec-e121-4d7a-9535-7f1788da23c1.png">
<img src="https://user-images.githubusercontent.com/107446796/180572229-56f3634d-3427-44be-8519-5d5346a8320c.png">

<h2>-sZ</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571748-b4358b6b-94a1-4ef7-901b-7858c97e2b64.png">
<img src="https://user-images.githubusercontent.com/107446796/180572236-e58ff400-e35d-4770-a7d2-9a9a2aa989b2.png">

<h2>-sV</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571842-135a9f06-bf60-4f92-8d02-7dca35076054.png">
<img src="https://user-images.githubusercontent.com/107446796/180571937-2690ca67-0893-44eb-bddd-344bf72706e4.png">

<h2>-sC</h2>
<img src="https://user-images.githubusercontent.com/107446796/180571872-4af824df-1181-4bb7-a6f6-ebff9bea47d9.png">
<img src="https://user-images.githubusercontent.com/107446796/180571928-a1d07beb-ec48-4201-8fd1-be9b44958d7f.png">
