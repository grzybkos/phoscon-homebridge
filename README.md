# Homebridge + Phoscon (Deconz) Setup for Raspberry Pi

This repository contains a simple setup to run **Phoscon (Deconz)** and **Homebridge** using **Docker Compose** on a Raspberry Pi. This guide will help you quickly rebuild your system in case of failure, with all configurations saved and easy to restore.

## Requirements

Before starting, ensure you have the following:

- **Raspberry Pi** with a compatible **ConBee II** USB stick.
- **Docker** and **Docker Compose** installed on your Raspberry Pi. [Guide here](https://docs.docker.com/engine/install/raspberry-pi-os/#install-using-the-repository)
- **Homebridge** for integrating your Zigbee devices with **Apple HomeKit**.

### Installation Instructions

### 1. **Clone the Repository**

First, clone this repository to your Raspberry Pi: (ideally in the `/applehome` directory)

```bash
git clone https://github.com/your-username/homebridge-phoscon.git
cd homebridge-phoscon
```

### 2. **Check Device Path for ConBee II**

The **ConBee II** USB stick will need to be correctly identified by Docker. To get the correct device path for your ConBee II stick, run the following commands:

1. **List the USB devices** connected to your Raspberry Pi:

   ```bash
   lsusb
   ```

   You should see something like:

   ```
   Bus 003 Device 088: ID 1cf1:0030 Dresden Elektronik ZigBee gateway [ConBee II]
   ```

2. **List the serial devices**:

   ```bash
   ls /dev/serial/by-id/
   ```

   This should display a path like:

   ```
   usb-dresden_elektronik_ingenieurtechnik_GmbH_ConBee_II_DE2698184-if00
   ```

   **Note**: Make sure the device name is correct in the `docker-compose.yml` file. This path will be used to link the ConBee II stick to the Docker container.

3. **Update the Device Path**: In the `docker-compose.yml` file, ensure that the device path is correctly set to match the output from the `ls /dev/serial/by-id/` command.

```yaml
DECONZ_DEVICE=/dev/serial/by-id/usb-dresden_elektronik_ingenieurtechnik_GmbH_ConBee_II_DE2698184-if00
```

### 3. **Update Docker Compose Configuration**

In the `docker-compose.yml`, ensure that the paths for Homebridge’s configuration are correct. The default paths are:

```yaml
volumes:
  - /applehome/homebridge/config:/homebridge/config
  - /applehome/homebridge/persist:/homebridge/persist
  - /applehome/homebridge/storage:/homebridge/storage
```

Replace `/applehome/homebridge` with your desired directory for Homebridge configuration and data storage.

### 4. **Start the Containers**

Once the device path is set and Docker Compose is configured, run the following command to start both containers:

```bash
sudo docker-compose up -d
```

This will download the necessary Docker images and start the **Phoscon** and **Homebridge** services.

### 5. **Access Phoscon and Homebridge UI**

- **Phoscon Web UI**: Access the Phoscon (Deconz) UI via:

  ```
  http://<raspberry_pi_ip>:8080
  ```

- **Homebridge UI**: Access the Homebridge UI via:
  ```
  http://<raspberry_pi_ip>:8581
  ```

### 6. **Pair Zigbee Devices to Phoscon**

To pair your Zigbee devices (e.g., lights, sensors, etc.) to Phoscon:

1. **Open the Phoscon UI** on `http://<raspberry_pi_ip>:8080`.
2. Navigate to **Settings > Zigbee > Pairing**.
3. Put your Zigbee device in pairing mode. For lights:
   - **On** for 6 seconds.
   - **Off** for 6 seconds.
   - **Leave it on** for successful pairing.

Once paired, you should see the devices listed in the **Phoscon UI**.

### 7. **Connect Deconz to Homebridge**

1. In the **Homebridge UI** on `http://<raspberry_pi_ip>:8581`, go to the **Plugins** section.
2. Search for and install the **Deconz** plugin (`homebridge-deconz`).
3. When asked for the **Deconz URL**, enter:

   ```
   localhost:8080
   ```

You can set this as a child bridge in the Homebridge UI.

4. **Restart Homebridge** to apply the changes.

Now, your Zigbee devices should show up in the **Accessories** tab in Homebridge UI.

## Troubleshooting

### **1. "Invalid Current Channel" Error in Deconz**

This seems to just happen when you first start the container. I did not see it after setting username/password in the Phoscon UI. If you see this error, you can probably ignore it.

### **2. Device Not Found in Homebridge**

If your Zigbee devices are not showing up in HomeKit:

1. Ensure that the **Deconz plugin** is correctly configured.
2. Ensure your **Homebridge** and **Phoscon** services are running by checking logs:

   ```bash
   sudo docker logs deconz
   sudo docker logs homebridge
   ```

3. Restart both services if necessary:
   ```bash
   sudo docker-compose down
   sudo docker-compose up -d
   ```

---

### Conclusion

This setup allows you to easily rebuild your Raspberry Pi and restore your **Zigbee devices** with **Homebridge** in case of failure. The use of **Docker Compose** ensures that the setup is consistent and easy to maintain. Make sure to back up your configuration and data regularly!

Feel free to contribute or improve this setup by submitting pull requests.

I dont know if the /applehome/homebridge directory is needed, but I have it in my setup.
