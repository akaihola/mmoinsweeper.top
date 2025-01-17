# Ansible Playbook for MMOInsweeper Server Provisioning

This repository contains Ansible playbooks and roles for provisioning a game server for MMOInsweeper, a Massively Multiplayer Online Infinite Minesweeper game.

## Game Information

- Game: MMOInsweeper (Massively Multiplayer Online Infinite Minesweeper)
- Game Repository: [https://github.com/akaihola/mmoinsweeper/](https://github.com/akaihola/mmoinsweeper/)

## Server Details

- OS: Ubuntu 24.04
- Hostname: mmoinsweeper.top
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
2. Install and configure Nginx as a reverse proxy
3. Set up Let's Encrypt for SSL/TLS certificates
4. Deploy the game binary and static content
5. Configure the game as a systemd service
6. Set up Nginx to forward requests to the Warp application and handle HTTPS

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

6. Create an Ansible vault file to store the root password and Certbot email:
   ```
   ansible-vault create vault.yml
   ```
   This will open your default text editor. When prompted, enter a secure password for the vault.

7. In the opened editor, add the root password and Certbot email to the vault file using this format:
   ```yaml
   vault_root_password: your_actual_root_password
   certbot_email: your_email@example.com
   ```
   Replace `your_actual_root_password` with the actual root password for the server and `your_email@example.com` with the email you want to use for Let's Encrypt notifications.
   Save and close the file when done.

   Note: If the editor doesn't open automatically, you may need to set the EDITOR environment variable:
   ```
   export EDITOR=nano  # or vim, or your preferred editor
   ```
   Then run the `ansible-vault create vault.yml` command again.

8. Update the `inventory.yml` file with the correct server IP if needed

9. Run the playbook:
    ```
    ansible-playbook -i inventory.yml provision_mmoinsweeper.yml --ask-vault-pass
    ```
    You'll be prompted to enter the vault password you created earlier.

    Note: If you encounter an error related to the vault password, ensure that you've correctly created and populated the vault.yml file. You can edit the vault file using:
    ```
    ansible-vault edit vault.yml
    ```

10. When you're done, deactivate the virtual environment:
    ```
    deactivate
    ```

11. After the playbook runs successfully, your MMOInsweeper server should be accessible via HTTPS at https://mmoinsweeper.top

## Security Notes

- Remember to secure the server further after initial provisioning
- Regularly update the server and deployed software
- Monitor the server for any suspicious activities

## License

This Ansible playbook is provided under the [MIT License](LICENSE).

## Contributing

Contributions to improve the playbook are welcome. Please submit pull requests or open issues for any enhancements or bug fixes.
