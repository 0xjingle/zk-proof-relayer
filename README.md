# ZK Proof Relayer

A permissionless Rust IMAP + SMTP server that reads email via imap, creates zk proofs of it, and replies to it. Uses IMAP to receive email and SMTP to send replies.

## Tests

### Test Chain

Test chain connection to verify that your connection to the chain works and simple tx's will send.

```sh
cargo test
```

## Run

First, run the relayer.

```sh
cargo run relayer
```

## Server Setup

### Enable IMAP in Gmail

Here's how to enable IMAP access and use App Passwords for your Gmail or Google Workspace account:

Enable IMAP:

a. Sign in to your Gmail or Google Workspace account.
b. Click the gear icon in the top-right corner and select "See all settings."
c. Go to the "Forwarding and POP/IMAP" tab.
d. In the "IMAP access" section, select "Enable IMAP."
e. Click "Save Changes."

Create an App Password (recommended):

a. Go to your Google Account settings: https://myaccount.google.com/
b. In the left-hand menu, click "Security."
c. In the "Signing in to Google" section, click on "App Passwords." (Note: This option will only be available if you have 2-Step Verification enabled.)
d. Click on "Select app" and choose "Mail" from the dropdown menu.
e. Click on "Select device" and choose the device you're using or select "Other" to enter a custom name.
f. Click "Generate."
g. Google will generate a 16-character App Password. Make sure to copy and save it securely, as you won't be able to see it again.

Now, when connecting to Gmail or Google Workspace via IMAP, use your email address as the "imap id" (username) and the generated App Password as the "password." If you have not enabled 2-Step Verification and are using "Less secure apps" access, you can use your regular email password instead of the App Password. However, using App Passwords is recommended for enhanced security.

### Enable ports in AWS

If there's an error, make sure your ports are open and traffic is allowed. This will be a massive pain in the \*\*\* so just stay with me while 3 hours of your life dissapate to nonsensical setups. Ensure that your EC2 instance has port 80 and 443 open in the security group to allow incoming traffic. You can check and update your security group settings from the AWS EC2 Management Console.

Step 0 is make sure to always add your IPv4 and IPv6 addresses to A and AAAA records respectively for both @ and www in DNS settings of the domain.

Then, enable inbound traffic. To do so, follow these steps:

0. Log in to the AWS Management Console.
1. Navigate to the EC2 Dashboard.
2. Select "Security Groups" from the left sidebar.
3. Find the security group associated with your EC2 instance and click on its name.
4. Click on the "Inbound rules" tab.
5. For the server, check if there are rules allowing traffic on ports 80 and 443. If not, add the rules by clicking on "Edit inbound rules" and then "Add rule". Choose "HTTP" for port 80 and "HTTPS" for port 443, and set the source to "Anywhere" or "0.0.0.0/0" (IPv4) and "::/0" (IPv6). For IMAP, click on "Add rule" and create new rules for the necessary IMAP ports (143 and 993) with the following settings:

- Type: Custom TCP
- Protocol: TCP
- Port Range: 143 (for IMAP) or 993 (for IMAPS)
- Source: Choose "Anywhere" for both IPv4 and IPv6 (0.0.0.0/0 for IPv4 and ::/0 for IPv6)

0. To rnable IPv6 support for your VPC (Virtual Private Cloud), go to the VPC Dashboard in the AWS Management Console, select your VPC, click on "Actions", and then click on "Edit CIDRs". Add an IPv6 CIDR block.
1. Enable IPv6 support for your subnet. Go to the "Subnets" section in the VPC Dashboard, select the subnet associated with your EC2 instance, click on "Actions", and then click on "Edit IPv6 CIDRs". Add an IPv6 CIDR block.
2. Enable IPv6 support for your EC2 instance. Go to the EC2 Dashboard in the AWS Management Console, select your instance, click on "Actions", and then click on "Manage IP Addresses". Assign an IPv6 address to your instance's network interface.
3. Update your instance's security group to allow inbound IPv6 traffic, if necessary.
4. If needed, configure your operating system to use IPv6. This step depends on the OS you're using. For most Linux distributions, IPv6 is enabled by default.

To enable the security group traffic, run these:

0. Log in to the AWS Management Console and navigate to the EC2 Dashboard: https://console.aws.amazon.com/ec2/
1. In the left-hand menu, click on "Security Groups" under the "Network & Security" section.
2. Locate the security group associated with your instance. You can find the security group in the instance details in the "Instances" section of the EC2 Dashboard.
3. Select the security group and click on the "Inbound rules" tab in the lower panel.
4. Click "Edit inbound rules."
5. Click "Add rule" to create a new rule.
6. Choose the desired rule type (e.g., "HTTP" for port 80, "HTTPS" for port 443, or "Custom TCP" for other ports). In the "Source" field, select "Anywhere-IPv6" to allow traffic from all IPv6 addresses. You can also specify a custom IPv6 range in CIDR notation (e.g., 2001:db8::/32).
7. Click "Save rules" to apply the changes.

Then in AWS EC2 shell, run

```sh
sudo ufw enable
sudo ufw allow http
sudo ufw allow https
sudo ufw allow ssh
sudo ufw allow 3000
```

Then run the certbot command again.
