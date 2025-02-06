---
layout: post
title:  "Unifi USG and Sky FTTP Setup"
date:   2025-02-06 13:20:00 +0100
---

# Context

- Sky Fibre to the Premises (FTTP) provides the WAN IPv4 address using DHCP.
- However it requires the modem to provide the DHCP Client Identifier (Option 61) in the format `user@skydsl|pass` (the actual values for `user` and `pass` are not important and can be provided exactly as shown).
- The Unifi Security Gateway (USG) does not provide a method to configure this option via the UI, therefore we must use the steps below to configure the DHCP client options for the WAN interface.

# Steps

1. On your local machine create `config.gateway.json` with the DHCP Client Identifier set e.g.:
    ```shell
    cat > ~/config.gateway.json <<EOF
    {
      "interfaces": {
        "ethernet": {
          "eth0": {
            "address": ["dhcp"],
            "description": "WAN",
            "dhcp-options": {
              "client-option": [
                "retry 60;",
                "send dhcp-client-identifier &quot;user@skydsl|pass&quot;;"
              ],
              "default-route": "update",
              "default-route-distance": "1",
              "name-server": "no-update"
            }
          }
        }
      }
    }
    EOF
    ```

2. Upload `config.gateway.json` to your CloudKey e.g.
    ```shell
    scp ~/config.gateway.json peter@gallagher.ie@192.168.1.2:/srv/unifi/data/sites/default/
    ```

3. Provision the USG
    1. Log into the Unifi console
    2. Click **Devices > USG-3P > Settings > Manage > Provision**
    3. Click **Confirm**

4. Your USG should shortly be provisioned and will connect via your FTTP ONT. If you do not get an IP address assigned immediately, you may need to restart the USG e.g. 
    1. Click **Devices > USG-3P > Settings > Manage > Restart**
    2. Click **Confirm**

5. (Optional) Verify the settings on your USG
    ```shell
    ssh unifi@192.168.1.1
    mca-ctrl -t dump-cfg | grep -A20 interfaces
    ```

# Alternative approach...

Alternatively you can connect to the USG via SSH and submit the following commands, but this is not guaranteed to persist after the device has been reprovisioned. So it is better to use the method above.

1. Connect to your USG via SSH e.g.:
    ```shell
    ssh unifi@192.168.1.1
    ```

2. Configure the DHCP client options (DHCP Option 61) e.g.:
    ```shell
    # Enter configuration mode
    configure

    # Enable DHCP for eth0
    set interfaces ethernet eth0 address dhcp

    # Configure DHCP Option 61
    set interfaces ethernet eth0 dhcp-options client-option "send dhcp-client-identifier &quot;user@skydsl|pass&quot;;"

    # Commit and save the settings
    commit
    save

    # Exit configuration mode
    exit

    # Reboot the USG
    reboot
    ```