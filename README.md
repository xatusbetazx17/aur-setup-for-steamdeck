# aur-setup-for-steamdeck

# Paru Steam Deck Setup Script by Xatusbetazx17

This repository contains a script to easily set up **paru**, an AUR helper, on a Steam Deck running SteamOS. The script automates the installation of `paru` (if not already installed), configures it for AUR package management, installs specified AUR packages, and optionally cleans up caches after installation.

---

## Features:
- **Automatic Paru Installation**: Installs `paru` if it's not already present.
- **AUR Package Management**: Easily installs specified AUR packages like `mpv`, `aria2`, and `fzf`.
- **Cache Cleanup**: Cleans up the package cache after installation to save space.
- **Customizable**: Modify the script to add or remove AUR packages as needed.

---

## Important Notes:
1. **SteamOS Read-Only Mode**: SteamOS uses a read-only filesystem by default, preventing system modifications. You must disable the read-only mode in Desktop Mode before running the script.
2. **Run the Script in Desktop Mode**: Ensure you switch to desktop mode on your Steam Deck to execute the script.

---

### Steps to Disable SteamOS Read-Only Mode:
1. **Switch to Desktop Mode**: Press the Steam button, go to "Power," and select "Switch to Desktop Mode."
2. **Open a Terminal**: Right-click on the desktop and open **Konsole** (the terminal).
3. **Disable Read-Only Mode** by running this command:
   ```bash
   sudo steamos-readonly disable




## 4 You can now make system modifications, such as installing paru and AUR packages.


## Script Instructions
This script will:

Check if paru is installed, and install it if necessary.
Install AUR packages using paru.
Clean up caches after installation.

## 1. Create the Script:
Open the terminal on your Steam Deck and create the script file:

nano ~/paru-setup.sh
## Paste the following script:

```bash
#!/bin/bash

# Function to check if a command exists
command_exists() {
    command -v "$1" >/dev/null 2>&1
}

# Step 1: Check if paru is installed, if not, install paru
if ! command_exists paru; then
    echo "Paru not found, installing paru..."

    # Clone the paru AUR repository
    git clone https://aur.archlinux.org/paru.git

    # Change to the paru directory
    cd paru

    # Build and install paru
    makepkg -si --noconfirm

    # Go back to the home directory and remove the cloned repository
    cd ..
    rm -rf paru

    echo "Paru installed successfully."
else
    echo "Paru is already installed."
fi

# Step 2: Check if 'fakeroot' is installed, if not, install it
if ! command_exists fakeroot; then
    echo "fakeroot not found, installing it..."
    sudo pacman -S --noconfirm fakeroot
else
    echo "fakeroot is already installed."
fi

# Step 3: Set signature level to 'Never' if not already set
echo "Checking if signaturelevel is set to 'Never'..."

if ! grep -q "SigLevel = Never" /etc/pacman.conf; then
    echo "Setting SigLevel to 'Never' in pacman.conf..."
    sudo sed -i 's/^SigLevel = .*/SigLevel = Never/g' /etc/pacman.conf
else
    echo "SigLevel is already set to 'Never'."
fi

# Step 4: Install Chaotic-AUR Key and Mirrorlist

# Import the primary key for Chaotic-AUR
echo "Importing the primary key for Chaotic-AUR..."
sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
sudo pacman-key --lsign-key 3056513887B78AEB

# Install Chaotic-AUR keyring and mirrorlist
echo "Installing Chaotic-AUR keyring and mirrorlist..."
sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst' \
'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'

# Step 5: Append Chaotic-AUR to pacman.conf if not already added
if ! grep -q "\[chaotic-aur\]" /etc/pacman.conf; then
    echo "Adding Chaotic-AUR repository to pacman.conf..."
    echo "[chaotic-aur]" | sudo tee -a /etc/pacman.conf
    echo "Include = /etc/pacman.d/chaotic-mirrorlist" | sudo tee -a /etc/pacman.conf
fi

# Step 6: Refresh the pacman database
echo "Refreshing pacman database..."
sudo pacman -Sy

# Step 7: Install AUR and Chaotic-AUR packages using paru (add your desired packages here)
echo "Installing AUR and Chaotic-AUR packages..."

# Example packages, you can add more as needed
paru -S --noconfirm mpv aria2 fzf
paru -S --noconfirm brave-bin

# Step 8: Clean up cache if necessary
echo "Cleaning up package cache..."
paru -Sc --noconfirm

echo "Paru and Chaotic-AUR setup and package installation completed!"


```




## 2. Save and Exit:
Press CTRL + O to save.
Press Enter to confirm.
Press CTRL + X to exit the text editor.
## 3. Make the Script Executable:
After creating the script, make it executable by running the following command:

chmod +x ~/paru-setup.sh

## 4. Test the Script:
To test the script and ensure it works as expected, run:

~/paru-setup.sh

## The script will:

Check if paru is installed.
Install paru if it is not already installed.
Install the listed AUR packages (mpv, aria2, fzf by default).
Clean the package cache after installation.
Customizing the Script:
You can easily customize which AUR packages the script installs. To do this:

Open the paru-setup.sh file in a text editor:



nano ~/paru-setup.sh
Add or remove the AUR packages you want to install in the script. For example:

bash
Copy code
paru -S --noconfirm mpv aria2 fzf
You can replace these packages with others from the AUR, depending on your needs.

Example AUR Packages:
mpv: A media player.
aria2: A lightweight multi-protocol download utility.
fzf: A command-line fuzzy finder.
Troubleshooting:
Permission Denied:
If you receive a "Permission Denied" error, ensure you've run the following command to make the script executable:

bash
Copy code
chmod +x ~/paru-setup.sh
Read-Only Filesystem:
Ensure the read-only mode is disabled by running the following command before executing the script:

bash
Copy code
sudo steamos-readonly disable
Re-enable Read-Only Mode (Optional):
Once the script has completed running, you can re-enable the read-only filesystem on SteamOS to protect against further system modifications:

bash
Copy code
sudo steamos-readonly enable

## Created by Xatusbetazx17

## License:
This project is licensed under the MIT License.
Feel free to modify the script or contribute to this repository!



