# Demo and Test vpn with docker-strongswan
The docker-compose.yml starts a strongswan server and an ubuntu system. The strongswan server and the ubuntu system share a common local network. Only the strongswan-server has an additional network interface and is accessible by remote systems outside the docker host. The ubuntu server is accessible only by establishing a vpn connection with the strongswan server.
## Setup details
Private network shared by strongswan-server and ubuntu system:
`10.1.0.0/24`

Private address strongswan:
`10.1.0.2`

Private address ubuntu:
`10.1.0.3`

Public address strongswan: `192.168.0.2`

## Starting the setup
```
docker compose up -d
```
### Fix routing of the ubuntu system
After starting the setup the routing of the ubuntu needs fixing:
```
docker exec --privileged ubuntu apt update
docker exec --privileged ubuntu apt install net-tools
docker exec --privileged ubuntu route del default
docker exec --privileged ubuntu route add default gw 10.1.0.2

```
## Using the setup
One can connect to the strongswan-server using any ipsec client with the following settings:
- Server: _Docker host_
- Remote id: `server.strongswan.org`
- Authentication: username/password
- Username: _username_
- Password: _password_

The following accounts are configured for accessing the strongswan server:
- Username:`jane`, password: `3s9RFGdWE5EW`
- Username: `hacker`, password: `K8FW9/N0VIAJ`

For the connection to suceed the `caKey.pem` Certificate needs to be imported into you ipsec client or operating system certificate store and set to trustworthy, if needed. E.g. for macOS this certificate must be in the keychain and set to trustworthy for all purposes. 

## Testing the setup
Once the connection is successfully established, the vpn client should be able to ping both `10.1.0.2` and `10.1.0.3` hosts within the docker environment.
