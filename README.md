<h1>AWS Multi-tier Cloud Project</h1>
<h3>DESCRIPTION</h3>
This project guides you on how to create and deploy a highly available, secure, and scalable multi-tier cloud architecture on AWS. This infrastructure can be adopted to host serverless applications, APIs, or microservices, and can be used in conjuction with multiple AWS services like S3, Amazon Polly, Amazon Translate, Amazon Comprehend, etc.<br>
<img src="https://i.imgur.com/lQHYZAa.png" height="80%" width="80%" alt="AWS VPC"/>
<b>NOTE: Diagram taken from the course "AWS Solutions Architect Associate SAA-C03" + "AWS Developer DVA-CO2" taught by <a href="https://lern.cantrill.io">Adrian Cantrill</a>. 
<h2>UTILITIES AND ENVIRONMENT USED</h2>
  <ul>
    <li>AWS EC2</li>
    <li>AWS VPC</li>
    <li>Amazon Linux 2023 OS</li>
  </ul>
<h2>PROGRAM WALK-THROUGH</h2><br>
<h3 align="center">Creating VPC "Skeleton":</h3><br>
  <b>1. First we have to make sure we're on the N. Virginia region on the aws console, you can select the region from the dropdown menu on the top right corner. After that we'll type VPC on the searchbar from the aws console, once on the VPC console you'll click on "create VPC".</b><br>
  <img src="https://i.imgur.com/K8gNODf.png" height="80%" width="80%" alt="AWS VPC"/><br>
  <b>2. Once in the VPC settings, you'll select "VPC only", and create a simple name for your VPC. Then, you'll type the IPv4 CIDR address that we'll use for the infrastructure "10.16.0.0/16" this will give us plenty of space to have thousands of IP address (65,536 available IP addresses to be specific). You'll also have to select "Amazon provided IPv6 CIDR block" to get IPv6 addresses for our instances, and click on "Create" </b><br>
  <img src="https://i.imgur.com/XkWtcEU.png" height="80%" width="80%" alt="AWS VPC"/><br>
  <b>3. After creating our VPC, we'll have to click on "Actions", and select "Edit VPC settings".</b>
  <img src="https://i.imgur.com/j7jSkJ0.png" height="80%" width="80%" alt="AWS VPC"/><br>
  <b>4. We'll have to enable DNS resolution and DNS hostnames on "DNS Settings". </b>
  <img src="https://i.imgur.com/hb3JYEH.png" height="80%" width="80%" alt="AWS VPC"/><br><br>
<h3 align="center">Creating the VPC's Subnets:</h3>
  <b>1. Next, we'll have to go to the subnet console (you'll find it on the left-side menu bar as "Subnets"). We'll select "Create a subnet" and we'll 4 subnets per each AZ (12 subnets in total), we'll name them as follows:</b><br><br>
  <ul>
    <li>sn-reserved-A</li>
    <li>sn-web-A (Public subnet)</li>
    <li>sn-app-A</li>
    <li>sn-db-A</li>
  </ul><br>
  <b>* You'll also have to create the subsequent db/web/app/reserved subnets for AZB and AZC as well. You can use the following table for the settings for each subnet:</b><br><br>
<table align="center">
  <tr>
    <th>Name</th>
    <th>IPv4 </th>
    <th>AZ</th>
    <th>IPv6 Last Digits</th>
  </tr>
  <tr>
    <td>sn-reserved-A</td>
    <td>10.16.0.0/20</td>
    <td>A</td>
    <td>00</td>
  </tr>
  <tr>
    <td>sn-db-A</td>
    <td>10.16.16.0/20</td>
    <td>A</td>
    <td>01</td>
  </tr>
  <tr>
    <td>sn-app-A</td>
    <td>10.16.32.0/20</td>
    <td>A</td>
    <td>02</td>
  </tr>
  <tr>
    <td>sn-web-A</td>
    <td>10.16.48.0/20</td>
    <td>A</td>
    <td>03</td>
  </tr>
  <tr>
    <td>sn-reserved-B</td>
    <td>10.16.64.0/20</td>
    <td>B</td>
    <td>04</td>
  </tr>
  <tr>
    <td>sn-db-B</td>
    <td>10.16.80.0/20</td>
    <td>B</td>
    <td>05</td>
  </tr>
  <tr>
    <td>sn-app-B </td>
    <td>10.16.96.0/20</td>
    <td>B</td>
    <td>06</td>
  </tr><tr>
    <td>sn-web-B</td>
    <td>10.16.112.0/20</td>
    <td>B</td>
    <td>07</td>
  </tr>
  <tr>
    <td>sn-reserved-C</td>
    <td>10.16.128.0/20</td>
    <td>C</td>
    <td>08</td>
  </tr>
  <tr>
    <td>sn-db-C</td>
    <td>10.16.144.0/20</td>
    <td>C</td>
    <td>09</td>
  </tr>
  <tr>
    <td>sn-app-C </td>
    <td>10.16.160.0/20</td>
    <td>C</td>
    <td>0A</td>
  </tr>
  <tr>
    <td>sn-web-C</td>
    <td>10.16.176.0/20</td>
    <td>C</td>
    <td>0B</td>
  </tr>
</table><br>
<b>2. You'll select the VPC that we created, type the subnet name, and select the right AZ for each subnet (us-east-1a for subnets A, us-east-1b for subnets B, us-east-1c for subnets C).</b><br>
<img src="https://i.imgur.com/hEeLzR8.png" height="80%" width="80%" alt="AWS VPC"/><br>
<b>3. Next we'll select "Manual Input" for both IPv4/IPv6. Copy and past the IPv4 address, and use the arrows for the IPv6 address (You'll have to click the downward arrow twice to get a /64, and then use the side arrows to change from 00-0B). </b><br>
<img src="https://i.imgur.com/h0Ss1WK.png" height="80%" width="80%" alt="AWS VPC"/><br>
<b>4. Once we have created all 12 subnets successfully, we'll select each subnet individually, click on "Actions", and select "Edit subnet settings". Once in there, we'll scroll down to "Auto-assign IP settings", and we'll enable IPv6 on all subnets. We'll also have to enable IPv4 for web-a/web-b/web-c, and save the settings. </b>
<img src="https://i.imgur.com/i5VueFL.png" height="80%" width="80%" alt="AWS VPC"/><br>
<h3 align="center">Creating the IGW & NATGW's:</h3><br>
