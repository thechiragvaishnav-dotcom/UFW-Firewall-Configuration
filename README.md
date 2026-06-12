# UFW-Firewall-Configuration
- if UFW already installed jump to [Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)
- open your linux terminal
  ![](images/image1.png)
- Check whether UFW is already install or not
  - <code>sudo ufw status</code>

  ![](images/image2.png)
- If you see command not found
  - <code>sudo apt install ufw</code>

  ![](images/image3.png)

## Content
- [Step 1 — Making Sure IPv6 is Enabled](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#step-1--making-sure-ipv6-is-enabled)
- [Step 2 — Setting Up Default Policies](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#step-2--setting-up-default-policies)
- [Step 3 — Allowing SSH Connections](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#step-3--allowing-ssh-connections)
- [Step 4 — Enabling UFW](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#step-4--enabling-ufw)
- [Step 5 — Allowing Other Connections](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#step-5--allowing-other-connections)

## Step 1 — Making Sure IPv6 is Enabled
In recent versions of Ubuntu, IPv6 is enabled by default. In practice that means most firewall rules added to the server will include both an IPv4 and an IPv6 version, the latter identified by <code>v6</code> within the output of UFW’s status command. To make sure IPv6 is enabled, you can check your UFW configuration file at <code>/etc/default/ufw</code>. Open this file using <code>nano</code> or your favorite command line editor:
- <code>sudo nano /etc/default/ufw</code> and press <code>Enter</code>

  ![](images/image4.png)
- Then make sure the value of <code>IPV6</code> is set to <code>yes</code>. It should look like this:

  ![](images/image5.png)
  - Save and close the file. If you’re using <code>nano</code>, you can do that by typing <code>CTRL+X</code>, then <code>Y</code> and <code>ENTER</code> to confirm.
  - When UFW is enabled in a later step of this guide, it will be configured to write both IPv4 and IPv6 firewall rules.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 2 — Setting Up Default Policies
If you’re just getting started with UFW, a good first step is to check your default firewall policies. These rules control how to handle traffic that does not explicitly match any other rules.

By default, UFW is set to deny all incoming connections and allow all outgoing connections. This means anyone trying to reach your server would not be able to connect, while any application within the server would be able to reach the outside world. You can then create specific <code>allow</code> rules as exceptions to this <code>deny</code> policy.

To make sure you’ll be able to follow along with the rest of this tutorial, you’ll now set up your UFW default policies for incoming and outgoing traffic.

To set the default UFW incoming policy to <code>deny</code>, run:
- <code>sudo ufw default deny incoming</code>

  ![](images/image6.png)

To set the default UFW outgoing policy to allow, run:
- <code>sudo ufw default allow outgoing</code>

  ![](images/image7.png)

These commands set the defaults to deny incoming and allow outgoing connections. These firewall defaults alone might suffice for a personal computer, but servers typically
need to respond to incoming requests from outside users. We’ll look into that next.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 3 — Allowing SSH Connections
If you were to enable your UFW firewall now, it would deny all incoming connections. This means that you’ll need to create rules that explicitly allow legitimate incoming connections — SSH or HTTP connections, for example — if you want your server to respond to those types of requests. If you’re using a cloud server, you will probably want to allow incoming SSH connections so you can connect to and manage your server.

1. Allowing the OpenSSH UFW Application Profile\
Upon installation, most applications that rely on network connections will register an application profile within UFW, which enables users to quickly allow or deny external  access to a service. You can check which profiles are currently registered in UFW with:\
<code>sudo ufw app list</code>\
\
![](images/image8.png)\
To enable the OpenSSH application profile, run:\
<code>sudo ufw allow OpenSSH</code>\
\
![](images/image9.png)\
This will create firewall rules to allow all connections on port 22, which is the port that the SSH daemon listens on by default.

2. Allowing SSH by Service Name\
Another way to configure UFW to allow incoming SSH connections is by referencing its service name: <code>ssh</code>.\
<code>sudo ufw allow ssh</code>\
\
![](images/image10.png)\
UFW knows which ports and protocols a service uses based on the <code>/etc/services</code> file.

3. Allowing SSH by Port Number\
Alternatively, you can write the equivalent rule by specifying the port instead of the application profile or service name. For example, this command works the same as the previous examples:\
<code>sudo ufw allow 22</code>\
\
![](images/image11.png)\
If you configured your SSH daemon to use a different port, you will have to specify the appropriate port. For example, if your SSH server is listening on port <code>2222</code>, you can use this command to allow connections on that port:\
<code>sudo ufw allow 2222</code>\
\
![](images/image12.png)\
Now that your firewall is configured to allow incoming SSH connections, you can enable it.

### Rate Limiting

To protect services like SSH from automated brute-force attacks, UFW includes a rate-limiting feature. When you apply a rate limit to a service, UFW tracks the frequency of connection attempts from each source IP address. If an IP address makes too many connections in a short period, UFW will temporarily block it. This is a more intelligent approach than simply allowing or denying traffic, as it distinguishes between normal use and behavior that is likely malicious.

To enable rate limiting for a service, you use the <code>limit</code> command instead of <code>allow</code>. The most common use case is securing SSH.
- <code>sudo ufw limit ssh</code>

  ![](images/image13.png)

This single command creates a rule that allows SSH connections, but with a condition: if an IP address attempts to initiate six or more connections within 30 seconds, UFW will deny further connections from that IP. It’s a simple and effective way to add an extra layer of security to services exposed to the internet.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 4 — Enabling UFW
Your firewall should now be configured to allow SSH connections. To verify which rules were added so far, even when the firewall is still disabled, you can use:
- <code>sudo ufw show added</code>

  ![](images/image14.png)
  - [what's the difference between this four RULES ?](Avoiding-Overlapping-Rules.md)

After confirming that you have a rule to allow incoming SSH connections, you can enable the firewall with:
- <code>sudo ufw enable</code>

  ![](images/image15.png)

You will receive a warning that says the command may disrupt existing SSH connections. You already set up a firewall rule that allows SSH connections, so it should be fine to continue. Respond to the prompt with <code>y</code> and hit <code>ENTER</code>.

The firewall is now active. Run the command to see the rules that are set.
- <code>sudo ufw status verbose</code>

  ![](images/image16.png)

The rest of this tutorial covers how to use UFW in more detail, like allowing or denying different kinds of connections.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 5 — Allowing Other Connections
At this point, you should allow all of the other connections that your server needs to respond to. The connections that you should allow depend on your specific needs. You already know how to write rules that allow connections based on an application profile, a service name, or a port; you already did this for SSH on port <code>22</code>. You can also do this for:
- HTTP on port 80, which is what unencrypted web servers use, using
    - <code>sudo ufw allow http</code>
    - <code>sudo ufw allow 80</code>
- HTTPS on port 443, which is what encrypted web servers use, using
    - <code>sudo ufw allow https</code>
    - <code>sudo ufw allow 443</code>
- Apache with both HTTP and HTTPS, using
    - <code>sudo ufw allow ‘Apache Full’</code>
- Nginx with both HTTP and HTTPS, using
    - <code>sudo ufw allow ‘Nginx Full’</code>

Don’t forget to check which application profiles are available for your server with <code>sudo ufw app list</code>.

There are several other ways to allow connections, aside from specifying a port or known service name. We’ll see some of these next.

### Specific Port Ranges
You can specify port ranges with UFW. Some applications use multiple ports, instead of a single port.

For example, to allow X11 connections, which use ports 6000-6007, use these commands:
- <code>sudo ufw allow 6000:6007/tcp</code>

  ![](images/image17.png)
  
- <code>sudo ufw allow 6000:6007/udp</code>

  ![](images/image18.png)
  
- When specifying port ranges with UFW, you must specify the protocol (<code>tcp</code> or <code>udp</code>) that the rules should apply to. We haven’t mentioned this before because not specifying the protocol automatically allows both protocols, which is OK in most cases.

### Specific IP Addresses
When working with UFW, you can also specify IP addresses within your rules. For example, if you want to allow connections from a specific IP address, such as a work or home IP address of <code>203.0.113.4</code>, you need to use the <code>from</code> parameter, providing then the IP address you want to allow:
- <code>sudo ufw allow from 203.0.113.4</code>

  ![](images/image19.png)

You can also specify a port that the IP address is allowed to connect to by adding <code>to any port</code> followed by the port number. For example, If you want to allow <code>203.0.113.4</code> to connect to port <code>22</code> (SSH), use this command:
- <code>sudo ufw allow from 203.0.113.4 to any port 22</code>

  ![](images/image20.png)

### Subnets
If you want to allow a subnet of IP addresses, you can do so using [CIDR notation](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#:~:text=CIDR%20notation%20is%20a%20compact,bits%20in%20the%20network%20mask.) to specify a netmask. For example, if you want to allow all of the IP addresses ranging from <code>203.0.113.1</code> to <code>203.0.113.254</code> you could use this command:
- <code>sudo ufw allow from 203.0.113.0/24</code>

  ![](images/image21.png)

Likewise, you may also specify the destination port that the subnet <code>203.0.113.0/24</code> is allowed to connect to. Again, we’ll use port <code>22</code> (SSH) as an example:
- <code>sudo ufw allow from 203.0.113.0/24 to any port 22</code>

  ![](images/image22.png)

### Connections to a Specific Network Interface
If you want to create a firewall rule that only applies to a specific network interface, you can do so by specifying “allow in on” followed by the name of the network interface.

You may want to look up your network interfaces before continuing. To do so, use this command:
- <code>ip addr</code>

  ![](images/image23.png)

he highlighted output indicates the network interface names. They are typically named something like <code>eth0</code> or <cdoe>enp3s2</code>.

So, if your server has a public network interface called <code>eth0</code>, you could allow HTTP traffic (port <code>80</code>) to it with this command:
- <code>sudo ufw allow in on eth0 to any port 80</code>

  ![](images/image24.png)

Doing so would allow your server to receive HTTP requests from the public internet.

Or, if you want your [MySQL](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-20-04) database server (port <code>3306</code>) to listen for connections on the private network interface <code>eth1</code>, for example, you could use this command:
- <code>sudo ufw allow in on eth1 to any port 3306</code>

  ![](images/image25.png)

This would allow other servers on your private network to connect to your MySQL database.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 6 — Denying Connections
If you haven’t changed the default policy for incoming connections, UFW is configured to deny all incoming connections. Generally, this simplifies the process of creating a secure firewall policy by requiring you to create rules that explicitly allow specific ports and IP addresses through.

However, sometimes you will want to deny specific connections based on the source IP address or subnet, perhaps because you know that your server is being attacked from there. Also, if you want to change your default incoming policy to **allow** (which is not recommended), you would need to create **deny** rules for any services or IP addresses that you don’t want to allow connections for.

To write deny rules, you can use the commands previously described, replacing **allow** with **deny**.

For example, to deny HTTP connections, you could use this command:
- <code>sudo ufw deny http</code>

  ![](images/image26.png)

Or if you want to deny all connections from <code>203.0.113.4</code> you could use this command:
- <code>sudo ufw deny from 203.0.113.4</code>

  ![](images/image27.png)

In some cases, you may also want to block outgoing connections from the server. To deny all users from using a port on the server, such as port <code>25</code> for SMTP traffic, you can use <code>deny out</code> followed by the port number:
- <code>sudo ufw deny out 25</code>

  ![](images/image28.png)

This will block all outgoing SMTP traffic on the server.

## [Back to Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/blob/main/README.md#content)

## Step 7 — Deleting Rules
