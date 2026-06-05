# UFW-Firewall-Configuration
- if UFW already installed jump to [Content](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/edit/main/README.md#content)
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

## Step 1 — Making Sure IPv6 is Enabled
In recent versions of Ubuntu, IPv6 is enabled by default. In practice that means most firewall rules added to the server will include both an IPv4 and an IPv6 version, the latter identified by <code>v6</code> within the output of UFW’s status command. To make sure IPv6 is enabled, you can check your UFW configuration file at <code>/etc/default/ufw</code>. Open this file using <code>nano</code> or your favorite command line editor:
- <code>sudo nano /etc/default/ufw</code>

  ![](images/image4.png)
- Then make sure the value of <code>IPV6</code> is set to <code>yes</code>. It should look like this:

  ![](images/image5.png)
  - Save and close the file. If you’re using <code>nano</code>, you can do that by typing <code>CTRL+X</code>, then <code>Y</code> and <code>ENTER</code> to confirm.
  - When UFW is enabled in a later step of this guide, it will be configured to write both IPv4 and IPv6 firewall rules.

## Step 2 — Setting Up Default Policies
If you’re just getting started with UFW, a good first step is to check your default firewall policies. These rules control how to handle traffic that does not explicitly match any other rules.

By default, UFW is set to deny all incoming connections and allow all outgoing connections. This means anyone trying to reach your server would not be able to connect, while any application within the server would be able to reach the outside world. You can then create specific <code>allow</code> rules as exceptions to this <code>deny</code> policy.

To make sure you’ll be able to follow along with the rest of this tutorial, you’ll now set up your UFW default policies for incoming and outgoing traffic.

To set the default UFW incoming policy to <code>deny</code>, run:
- <code>sudo ufw default deny incoming</code>

  ![](images/image6.png)
