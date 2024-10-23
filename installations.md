
# Install Proxmox VE: Step-by-Step Guide

## 1. Ensure Your Server is Running Debian
If you're using a fresh install of Debian 12 (bookworm), you can proceed directly with the Proxmox setup.

---

## 2. Add the Proxmox Repository
Start by adding the Proxmox VE repository.

```bash
echo "deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription" | tee /etc/apt/sources.list.d/pve-install-repo.list
```

---

## 3. Add the Proxmox GPG Key
Download and add the Proxmox GPG key.

```bash
wget https://enterprise.proxmox.com/debian/proxmox-release-bookworm.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bookworm.gpg
```

---

## 4. Update Your Package List
Update the package lists to include Proxmox.

```bash
apt update
```

---

## 5. Install Proxmox VE
Install Proxmox VE along with its required dependencies.

```bash
apt install proxmox-ve postfix open-iscsi
```

- When asked to configure **Postfix**, choose "Internet Site" if prompted.

---

## 6. Test Proxmox Installation
After the reboot, access the Proxmox web interface via your browser.

- Open your browser and go to: `https://<your-server-ip>:8006` (e.g., `https://192.168.0.100:8006`).
- If you can access the web interface and log in, then Proxmox is working correctly.


# Install Grafana: Step-by-Step Guide

## 1. Download the GPG Key for Grafana
This command will fetch and store the GPG key for Grafana in the correct keyring directory:

```bash
mkdir -p /etc/apt/keyrings
wget -qO- https://packages.grafana.com/gpg.key | gpg --dearmor | tee /etc/apt/keyrings/grafana-archive-keyring.gpg > /dev/null
```

---

## 2. Add the Grafana APT Repository
Now, add the Grafana repository to your APT sources:

```bash
echo "deb [signed-by=/etc/apt/keyrings/grafana-archive-keyring.gpg] https://packages.grafana.com/oss/deb stable main" | tee /etc/apt/sources.list.d/grafana.list
```

---

## 3. Update Your Package List
Update your APT package list to reflect the new Grafana repository:

```bash
apt update
```

---

## 4. Install Grafana
Install Grafana using APT:

```bash
apt install grafana
```

---

## 5. Start and Enable Grafana
After installation, start the Grafana service and enable it to start on boot:

```bash
systemctl start grafana-server
systemctl enable grafana-server
```

---

## 6. Access Grafana
Once the service is running, access Grafana in your browser at:

```text
http://192.168.0.26:3000
```

- The default login credentials are:

  - **Username**: admin
  - **Password**: admin


# Integrating Netdata with Prometheus

To integrate Netdata with Prometheus, you'll need to add a new scrape job for Netdata in the `prometheus.yml` file.

---

## 1. Open your `prometheus.yml` configuration file

```bash
nano prometheus.yml
```

---

## 2. Add a new job configuration for Netdata under `scrape_configs`

Below is an example of how you can configure Prometheus to scrape Netdata metrics:

```yaml
scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]

  - job_name: "netdata"
    static_configs:
      - targets: ["192.168.0.26:19999"]
```

This will tell Prometheus to scrape metrics from your Netdata instance running on port 19999.

---

## 3. Save the file and restart Prometheus

To apply the changes, save the `prometheus.yml` file and restart Prometheus:

```bash
./prometheus --config.file=prometheus.yml &
```


# Alternative Installation for Netdata (using APT)

## 1. Add the Netdata Repository
Add the Netdata repository by running the following command:

```bash
bash <(curl -Ss https://packagecloud.io/install/repositories/netdata/netdata/script.deb.sh)
```

---

## 2. Install Netdata
Once the repository is added, install Netdata:

```bash
apt install netdata
```

---

## 3. Start Netdata
After installation, start and enable the Netdata service:

```bash
systemctl start netdata
systemctl enable netdata
```

---

## 4. Access Netdata
You can now access Netdata from your browser by navigating to:

```text
http://<your-server-ip>:19999
```
Replace `<your-server-ip>` with the actual IP address of your server.

---

This method uses the official APT repository for Netdata and should avoid any issues with script-based installation.


# Install Netdata: Step-by-Step Guide

## 1. Add the Netdata Repository
Add the Netdata repository by running the following command:

```bash
bash <(curl -Ss https://packagecloud.io/install/repositories/netdata/netdata/script.deb.sh)
```

---

## 2. Install Netdata
Once the repository is added, install Netdata:

```bash
apt install netdata
```

---

## 3. Start Netdata
After installation, start and enable the Netdata service:

```bash
systemctl start netdata
systemctl enable netdata
```

---

## 4. Access Netdata
You can now access Netdata from your browser by navigating to:

```text
http://<your-server-ip>:19999
```
Replace `<your-server-ip>` with the actual IP address of your server.


# Install MinIO on Your Home Server

## Download MinIO:
```bash
wget https://dl.min.io/server/minio/release/linux-amd64/minio
chmod +x minio
mv minio /usr/local/bin/
```

## Create MinIO directories:
```bash
mkdir -p /mnt/data
```

## Create a MinIO service:

Create the systemd service file:
```bash
nano /etc/systemd/system/minio.service
```

Add the following content:
```ini
[Unit]
Description=MinIO
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target

[Service]
User=root
Group=root
WorkingDirectory=/usr/local/
ExecStart=/usr/local/bin/minio server --address :9000 /mnt/data
Restart=always
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
```

## Start and enable MinIO:
```bash
systemctl daemon-reload
systemctl start minio
systemctl enable minio
```

## Access MinIO:

MinIO will now be running on port 9000. You can access it in your browser by going to:
```arduino
http://<your-server-ip>:9000
```

The default access key and secret key will be generated on startup. You can view the output of the MinIO service by running:
```bash
journalctl -u minio -f
```

## Configure MinIO (Optional):
You can change the access keys by exporting the `MINIO_ACCESS_KEY` and `MINIO_SECRET_KEY` environment variables and restarting the MinIO service.

