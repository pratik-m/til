# Access localhost on the same network

If you have a FastAPI/Flask app that you need to expose to your local network, perform the below steps: 

1. Get your computer's local address: `hostname -i`
2. Run FastAPI/Flask app with host `0.0.0.0`
3. Open firewall port `sudo ufw allow <port>/tcp`
4. Access from other device/computer: `http://<your_ip>:<port>`

> Use `0.0.0.0` instead of `127.0.0.1` to bind to all network interfaces, not just `localhost`.
