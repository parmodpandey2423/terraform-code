# terraform-code
#!/bin/bash

# Variables
GITHUB_REPO_URL="https://github.com/parmodpandey2423/terraform-code.git"
DEST_DIR="/home/parmodpandey24/terraform"

# Install Git if not installed
sudo yum install -y git

KEY_PATH="$HOME/.ssh/id_rsa"
EMAIL="parmodpandey24@gmail.com"

# Check if the SSH key already exists
if [ -f "$KEY_PATH" ]; then
  echo "SSH key already exists at $KEY_PATH. Skipping key generation."
else
  # Generate the SSH key without a passphrase
  ssh-keygen -t rsa -b 4096 -C "$EMAIL" -f "$KEY_PATH" -N ""
  echo "SSH key created at $KEY_PATH with no passphrase."

# Display the public key
cat "$KEY_PATH.pub"

 echo "Go to GitHub > Settings > SSH and GPG keys > New SSH key"
  echo "Press Y once you've added the key to GitHub."
  read -p "Press Y to continue after adding the key: " proceed

  if [[ "$proceed" != "Y" && "$proceed" != "y" ]]; then
    echo "You must add the SSH key to GitHub before continuing."
    exit 1
  fi
fi

ssh -o StrictHostKeyChecking=no -T git@github.com
git clone "$GITHUB_REPO_URL" "$DEST_DIR"


# Clone or pull the repo
if [ -d "$DEST_DIR" ]; then
  echo "Repo already cloned, pulling latest changes..."
  git branch -M main
  git push -u origin main
  cd "$DEST_DIR" && git pull
else
  echo "Cloning the repo..."
fi
