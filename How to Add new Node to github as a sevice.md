# How to Add new Node to github as a sevice.
```
cd ~/actions-runner

# (Optional but recommended) ensure not running as root
whoami

# Install & start the service (runs as root if you don't specify a user)
sudo ./svc.sh install
sudo ./svc.sh start

# Check it
sudo ./svc.sh status
# or
systemctl status "actions.runner.Clover-jio.*.service"
```
