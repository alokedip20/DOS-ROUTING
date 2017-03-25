<h3>Set-up:</h3> 
<p>
	For this assignment we need atleast five laptops. Among them two devices will be connected via hub-1 (they will be in same network having netID-1) and rest two will be connected via hub-2 (they will be having netID-2). hub-1 and hub-2 will be connected with the laptop with two different NICs (PCI & USB in my case) in which "Router programme" will run. In virtual box I have to select two adapters and each of these adapters have been bridged with corresponding NICs(PCI + USB). By this complete network set up we have created a private network to reduce the unintended network traffic to prevent the "Crash is programme execution".</p>

<h3>Packet Drivers installation:</h3>
	<p>
		I have to install two packet drivers for two different NICs in my virtual box. For doing this I have used the patched version of <a href="http://unix.oppserver.net/vmware/unix/fixpcnt.com">"PCNTPK.COM" </a> The respective Commands to install packet drivers for two virtual adapters are as below:
		<ol>
			<li> fixpcnt.com  (it will create pcntpk2.com ==>patched version of pcntpk.com)</li>
			<li> pcntpk2.com int=0x60 ioaddr=0 (it will install packet driver for    adapter[			0]==>1st ADP)</li>
			<li> pcntpk2.com int=0x70 ioaddr=1 (it will install packet driver for adapter[1]==>2nd ADP)</li>
		</ol>
		<b>*** For doing (2 , 3) at startup I have inserted these two commands in autoexec.bat in 		DOS.</b>
	</p>

<h3>Packet Structure:</h3>
		<ul>
			<li> packet[0] -> packet[5] ==> destination mac address</li>
			<li> packet[6] -> packet[11] ==> source mac address</li>
			<li> packet[12] -> packet[13] ==> packet Type (in my case 0xABCD)</li>
			<li> packet[14] -> packet[15] ==> destination iip</li>
			<li> packet[16] -> packet[17] ==> source iip</li>
			<li> packet[18] -> packet[18+size_of_data] ==> data</li>
			<li> packet[size_of_data+19] -> packet[MAX-1] ==> padded by NULL (0x00)</li>
		</ul>

<h3>Experiment:</h3>
	<p>	
	In router programme I have maintained a ARP table,which has two fields 1. iip[2] , 2. mac[6]. The entries have been read from a file ("routing.txt" ==>  <b>{netID:hostID}+{mac}</b>).This ARP table will be populated whenever the programme will be executed. I have also maintained same ARP table in each client so that iip to mac mapping can be done also in client side so that when clients want to communicate within the network they will send those packets directly to the destination device,but not to the router.But when client with netID-1 wants to communicate client with netID-2 then (by that ARP table) the client will send the packet to corresponding router adapter and in router it will first try to find if there is an entry for the comming destination iip, if any entry will be found then it will put the destination into the packet and send that packet through paticular adapter. If  no such entry will be found then ERROR messege will be displayed in router side.</p>

<h3>Code:</h3>
	<p>	
	The main file for the "Router" is <b>"ROUTER.C"</b>. In this file I have included two extra headers 
		<ol>              
	    	<li>
	    		<b>"MY_NET.H"</b> ===> all those packet driver APIs has been defined in this header.  
			</li>
	   		<li> 
	   			<b>"ROUTING.H"</b> ===> Main routing algorithm ,Creation of ARP table have been implemented in this header.
	 	  	</li>
		</ol>
		<h4> Build :-</h4>
		<ul>
			<li> After installation of Turbo C++ all the necessary binary files like <b>"tc.exe -> code editor , tcc.exe -> compiler</b> will be located into the <b>tc</b> directory". So for compiling any .c file from any directory I have added this <b>BIN</b> directory to the environment variables (located into the <b>C:\autoexec.bat</b>).</li>
			<li> Copy "MY_NET.H" and "ROUTING.H" into the same directory where ROUTER.C will be created.</li>
			<li> <b> Compilation :- </b> tcc -ml "file_name"</li>
			<li> Run the executable file :- ROUTER.EXE</li>
		</ul>
	</p>

<h3>Extra software:</h3>
<p>
	<ol>
		<li> 
			For transferring file between linux host and Virtual Box (MS-DOS) I have used 	"mtcp->FTP".
		</li>
		<li> 
			For debugging purpose, (To check if packets have been received by the host device or have been sent from the host device) ,I have used <b>"wireshark"</b>
		</li>
	</ol>
</p>
