# Ansible Playbook for MMOInsweeper Server Provisioning

This repository contains Ansible playbooks and roles for provisioning a game server for MMOInsweeper, a Massively Multiplayer Online Infinite Minesweeper game.

## Game Information

- Game: MMOInsweeper (Massively Multiplayer Online Infinite Minesweeper)
- Game Repository: [https://github.com/akaihola/mmoinsweeper/](https://github.com/akaihola/mmoinsweeper/)

## Server Details

- OS: Ubuntu 24.04
- Hostname: mmoinsweeper.net
- IP Address: 212.227.230.223
- Initial Access: Root user with known password

## Deployment Architecture

1. **Game Binary**: Rust-compiled standalone executable
2. **Static Content**: Directory structure containing HTML, CSS, and JavaScript files
3. **Web Server**: Nginx
   - Serves static content over HTTPS
   - SSL/TLS termination with Let's Encrypt managed certificates
   - Proxies WebSocket SSL traffic to the game binary
4. **Game Service**: Runs as a systemd service
   - Dedicated Unix user account: "mmoinsweeper"

## Ansible Playbook Features

This playbook will:

1. Set up the "mmoinsweeper" user account
2. Install and configure Nginx
3. Set up Let's Encrypt for SSL/TLS certificates
4. Deploy the game binary and static content
5. Configure the game as a systemd service
6. Set up Nginx as a reverse proxy for both static content and WebSocket traffic

## Usage

1. Ensure you have an SSH key pair. If you don't, create one:
   ```
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ```
   This will create a new SSH key pair in `~/.ssh/id_ed25519` (private key) and `~/.ssh/id_ed25519.pub` (public key).

2. Create a Python virtual environment:
   ```
   python3 -m venv mmoinsweeper_env
   ```

3. Activate the virtual environment:
   - On Linux or macOS:
     ```
     source mmoinsweeper_env/bin/activate
     ```
   - On Windows:
     ```
     mmoinsweeper_env\Scripts\activate
     ```

4. Install the required dependencies:
   ```
   pip install -r requirements.txt
   ```

5. Install sshpass (required for initial root login):
   - On Ubuntu/Debian:
     ```
     sudo apt-get install sshpass
     ```
   - On macOS (using Homebrew):
     ```
     brew install hudochenkov/sshpass/sshpass
     ```
   - On other systems, consult your package manager or install from source.

6. Create an Ansible vault file to store the root password:
   ```
   ansible-vault create vault.yml
   ```
   This will open your default text editor. When prompted, enter a secure password for the vault.

5. In the opened editor, add the root password to the vault file using this format:
   ```yaml
   vault_root_password: your_actual_root_password
   ```
   Replace `your_actual_root_password` with the actual root password for the server.
   Save and close the file when done.

   Note: If the editor doesn't open automatically, you may need to set the EDITOR environment variable:
   ```
   export EDITOR=nano  # or vim, or your preferred editor
   ```
   Then run the `ansible-vault create vault.yml` command again.

6. Update the `inventory.yml` file with the correct server IP if needed

8. Ensure your SSH public key is added to the server's authorized_keys file for the root user. If not, you can add it manually or use ssh-copy-id:
   ```
   ssh-copy-id -i ~/.ssh/id_ed25519.pub root@212.227.230.223
   ```

10. Run the playbook:
    ```
    ansible-playbook -i inventory.yml provision_mmoinsweeper.yml --ask-vault-pass
    ```
    You'll be prompted to enter the vault password you created earlier.

    Note: If you encounter an error related to the vault password, ensure that you've correctly created and populated the vault.yml file. You can edit the vault file using:
    ```
    ansible-vault edit vault.yml
    ```

11. When you're done, deactivate the virtual environment:
   ```
   deactivate
   ```

## Security Notes

- Remember to secure the server further after initial provisioning
- Regularly update the server and deployed software
- Monitor the server for any suspicious activities

## License

This Ansible playbook is provided under the [MIT License](LICENSE).

## Contributing

Contributions to improve the playbook are welcome. Please submit pull requests or open issues for any enhancements or bug fixes.
