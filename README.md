# UFW-Firewall-Configuration
- open your linux terminal
  ![](images/image1.png)
- Check whether UFW is already install or not
  - <code>sudo ufw status</code>

  ![](images/image2.png)
- If you see command not found
  - <code>sudo apt install ufw</code>

  ![](images/image3.png)

## Content
- [Step 1 — Making Sure IPv6 is Enabled](https://github.com/thechiragvaishnav-dotcom/UFW-Firewall-Configuration/edit/main/README.md#step-1--making-sure-ipv6-is-enabled)

## Step 1 — Making Sure IPv6 is Enabled
In recent versions of Ubuntu, IPv6 is enabled by default. In practice that means most firewall rules added to the server will include both an IPv4 and an IPv6 version, the latter identified by <code>v6</code> within the output of UFW’s status command. To make sure IPv6 is enabled, you can check your UFW configuration file at <code>/etc/default/ufw</code>. Open this file using <code>nano</code> or your favorite command line editor:
