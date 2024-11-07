# Linux_adm-portfolio_Automated-Linux-Deployment-Script
#!/bin/bash
# Ensure the script is run as root
if [[ "$EUID" -ne 0 ]]; then
   echo "Please run as root"
   exit 1
fi

# Update the package list and upgrade any existing packages
echo "Updating package list and upgrading installed packages..."
apt update && apt upgrade -y

# Install essential packages
echo "Installing essential packages..."
apt install -y curl wget vim git net-tools build-essential

# Set up a new user with sudo privileges
NEW_USER="deployer"
echo "Creating a new user '$NEW_USER'..."
useradd -m -s /bin/bash -G sudo "$NEW_USER"
echo "$NEW_USER:password" | chpasswd

# Enable sudo without password for the new user (optional)
echo "$NEW_USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Configure firewall (UFW) to allow SSH and HTTP(S) traffic
echo "Configuring firewall..."
apt install -y ufw
ufw allow OpenSSH
ufw allow 80
ufw allow 443
ufw enable

# Install and configure Nginx
echo "Installing Nginx..."
apt install -y nginx
systemctl enable nginx
systemctl start nginx

# Set up SSH keys for the new user (recommended for secure SSH access)
echo "Setting up SSH keys for $NEW_USER..."
su - "$NEW_USER" -c "mkdir -p ~/.ssh && chmod 700 ~/.ssh"
echo "your-public-key-here" > /home/"$NEW_USER"/.ssh/authorized_keys
chmod 600 /home/"$NEW_USER"/.ssh/authorized_keys
chown -R "$NEW_USER":"$NEW_USER" /home/"$NEW_USER"/.ssh

# Install Docker (if required)
echo "Installing Docker..."
apt install -y docker.io
usermod -aG docker "$NEW_USER"
systemctl enable docker
systemctl start docker

# Clean up
echo "Cleaning up..."
apt autoremove -y && apt autoclean -y

echo "Deployment complete! The server is configured and ready to use."

#!/bin/bash

# Ensure the script is run as root
if [[ "$EUID" -ne 0 ]]; then
   echo "Please run as root"
   exit 1
fi

# Update the package list and upgrade any existing packages
echo "Updating package list and upgrading installed packages..."
apt update && apt upgrade -y

# Install essential packages
echo "Installing essential packages..."
apt install -y curl wget vim git net-tools build-essential

# Set up a new user with sudo privileges
NEW_USER="deployer"
echo "Creating a new user '$NEW_USER'..."
useradd -m -s /bin/bash -G sudo "$NEW_USER"
echo "$NEW_USER:password" | chpasswd

# Enable sudo without password for the new user (optional)
echo "$NEW_USER ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Configure firewall (UFW) to allow SSH and HTTP(S) traffic
echo "Configuring firewall..."
apt install -y ufw
ufw allow OpenSSH
ufw allow 80
ufw allow 443
ufw enable

# Install and configure Nginx
echo "Installing Nginx..."
apt install -y nginx
systemctl enable nginx
systemctl start nginx

# Set up SSH keys for the new user (recommended for secure SSH access)
echo "Setting up SSH keys for $NEW_USER..."
su - "$NEW_USER" -c "mkdir -p ~/.ssh && chmod 700 ~/.ssh"
echo "your-public-key-here" > /home/"$NEW_USER"/.ssh/authorized_keys
chmod 600 /home/"$NEW_USER"/.ssh/authorized_keys
chown -R "$NEW_USER":"$NEW_USER" /home/"$NEW_USER"/.ssh

# Install Docker (if required)
echo "Installing Docker..."
apt install -y docker.io
usermod -aG docker "$NEW_USER"
systemctl enable docker
systemctl start docker

# Clean up
echo "Cleaning up..."
apt autoremove -y && apt autoclean -y

echo "Deployment complete! The server is configured and ready to use."
# Example Usage
This script is designed for quick deployment on virtual machine or cloud instance, Here's an example of how you might use it:

1- clone the repository
2- Run the script as describe above
3- Comfirm access and settings by logging in as the new user via SSH

# Troubleshooting
- User Permissions: Ensure the sudo group is correctly assigned to the new user.
- Firewall: If SSH access is blocked, comfirm that firawall rules are configured to allow SSH
- Network configuration: Verify IP settings if using a static configuration.

# Contributing
Contributions are welcome! fell free to open issues for feedback or improvements, and submit pull requests.

# License
This project is licensed under the MIT license. See the License file for details

# Contact
For further questions or support, please contact darrylnnon@gmail.com
