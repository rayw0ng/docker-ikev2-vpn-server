# IKEv2 VPN Server on Docker

## Usage

### 0. Build latest image

    docker build -t vpn/ikev2 .

### 1. Start the IKEv2 VPN Server

    docker run -d --name ikev2-vpn-server --restart=always --privileged -p 500:500/udp -p 4500:4500/udp vpn/ikev2

### 2. Generate the .mobileconfig (for iOS / OS X)

    docker run -i -t --rm --volumes-from ikev2-vpn-server -e "HOST=vpn1.example.com" vpn/ikev2 generate-mobileconfig > ikev2-vpn.mobileconfig

*Be sure to replace `vpn1.example.com` with your own domain name and resolve it to you server's IP address. Simply put an IP address is supported as well (and enjoy an even faster handshake speed).*

Transfer the generated `ikev2-vpn.mobileconfig` file to your local computer via SSH tunnel (`scp`) or any other secure methods.

### 3. Install the .mobileconfig (for iOS / OS X)

- **iOS 9 or later**: AirDrop the `.mobileconfig` file to your iOS 9 device, finish the **Install Profile** screen;

- **OS X 10.11 El Capitan or later**: Double click the `.mobileconfig` file to start the *profile installation* wizard.

## Usage for Windows

### Server

#### 1. Build docker image

    docker build -t vpn/ikev2 github.com/tsl0922/docker-ikev2-vpn-server

#### 2. Start the IKEv2 VPN Server

    docker run -d --name ikev2-vpn-server --restart=always --privileged -p 500:500/udp -p 4500:4500/udp -e "CERT_CN=Your_Server_IP_Address" -e "VPN_USER=Your_User_Name" -e "VPN_PASSWORD=Your_Password" vpn/ikev2
    
*Replace `Your_Server_IP_Address`, `Your_User_Name` and `Your_Password`.*

#### 3. Copy Cert file

    docker cp ikev2-vpn-server:/etc/ipsec.d/cacerts/caCert.pem ./
    
Copy this `caCert.pem` file to your Windows computer.

### Client

#### 1. Install Cert

    certutil –addstore -enterprise –f “Root” caCert.pem
    
#### 2. Add VPN Connection

#### 3. Bug fix

You may need to fix this.

    get-vpnconnection    # view vpn connections
    set-vpnconnection "vpn-name" -splittunneling $false    #disable split tunneling

## Technical Details

Upon container creation, a *shared secret* was generated for authentication purpose, no *certificate*, *username*, or *password* was ever used, simple life!

## License

Copyright (c) 2016 Mengdi Gao, This software is licensed under the [MIT License](LICENSE).

---

\* IKEv2 protocol requires iOS 8 or later, Mac OS X 10.11 El Capitan is supported as well.

\* Install for **iOS 8 or later** or when your AirDrop fails: Send an E-mail to your iOS device with the `.mobileconfig` file as attachment, then tap the attachment to bring up and finish the **Install Profile** screen.
