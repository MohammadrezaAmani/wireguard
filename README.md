# Wireguard Utilities

Wireguard Utilities is a versatile module designed to simplify the configuration process for [WireGuard](https://wireguard.com) VPN setups, catering to both server-side and client-side configurations.

## Quick Start

Setting up a WireGuard server is effortless with the following code snippet:

```python
from wireguard import Server

server = Server('myvpnserver.com', '192.168.24.0/24', address='192.168.24.1')

# Write out the server configuration to the default location: /etc/wireguard/wg0.conf
server.config.write()
```

Creating a client within the previously established server is equally straightforward:

```python
peer = server.peer('my-client')

# Output this peer's configuration for copying to the peer device
print(peer.config.local_config)

# Rewrite the server configuration file to include the newly created peer
server.config.write()
```

For standalone client setup, utilize the following code:

```python
from wireguard import Peer

peer = Peer('my-client', '192.168.24.0/24', address='192.168.24.45')

# Write out the peer configuration to the default location: /etc/wireguard/wg0.conf
peer.config.write()
```

**Note**: By default, both server and peer configuration files share the same name. This convention is suitable as they would typically reside on different machines without conflicting with each other. Exercise caution when generating peer configurations on a server node or any node with pre-existing WireGuard configurations at the default file location.

## Other Features

You can streamline the setup by passing both the address and subnet combined to `Server`:

```python
# Define the subnet as 192.168.24.0/24 and the server's IP as 192.168.24.51
server = Server('myvpnserver.com', '192.168.24.51/24')
```

Additionally, a custom JSON encoder, `wireguard.utils.json.JSONEncoder`, is provided for enhanced functionality. It can be employed as the value for `cls` in any call to `json.dumps()`. Conveniently, it is automatically utilized by both peers and servers when invoking the `.json()` method. Any provided arguments are seamlessly passed through to `json.dumps()`:

```python
server.json(sort_keys=True, indent=4)
```

which yields:

```json
{
    "address": [
        "192.168.24.51"
    ],
    "allowed_ips": [
        "192.168.24.51/32"
    ],
    "description": "myvpnserver.com",
    "dns": [],
    "endpoint": null,
    "interface": "wg0",
    "keepalive": null,
    "mtu": null,
    "peers": [],
    "post_down": [],
    "post_up": [],
    "pre_down": [],
    "pre_up": [],
    "preshared_key": null,
    "private_key": "+ZNzpdQKgnuFHGtwDn3EzTZB5J8kYis+UMQ4FALSvtI=",
    "public_key": "AvteU+hwrtJW4QvDy/xH+rxXzNHQ33LclcQ646xwmFw=",
    "subnet": [
        "192.168.24.0/24"
    ],
    "table": null
}
```

**Note**: If the `cls` argument is passed to the `Peer.json()` method, it will override the use of the included custom JSON encoder. Consequently, appropriate handling of objects within the JSON encoder being passed is required.
