# Bastion — jump host (gate) based on OpenSSH Server (sshd)

> A [bastion host](https://en.wikipedia.org/wiki/Bastion_host) is a special purpose computer on a network specifically designed and configured to withstand attacks. The computer generallyhosts a single application, for example a proxy server, and all other services are removed or limited to reduce the threat to the computer. It is hardened in this manner primarily due to its location and purpose, which is either on the outside of a firewall or in a demilitarised zone (`DMZ`) and usually involves access from untrusted networks or computers. 

## Useful cases

Bastion is an isolated `Docker` image that can work as a link between `Public` and `Private` network. It can be also useful for reverse `SSH` tunneling for a host behind a `NAT`. This image based on `Alpine Linux` last version.

## Usage

###  Describing ENV variables

* `PUBKEY_AUTHENTICATION [true | false]` - Specifies whether public key authentication is allowed. The default is `true`. Note that this option applies to protocol version 2 only.

* `AUTHORIZED_KEYS [/relative/or/not/path/to/file]` - Specifies the file that contains the public keys that can be used for user authentication. `AUTHORIZED_KEYS` may contain tokens of the form `%T` which are substituted during connection setup. The following tokens are defined: `%%` is replaced by a literal `%`, `%h` is replaced by the home directory of the user being authenticated, and `%u` is replaced by the username of that user. After expansion, `AUTHORIZED_KEYS` is taken to be an absolute path or one relative to the user's home directory. The default file is `authorized_keys` and the default home directory is `/var/lib/bastion` and should be present by Docker volume mount by `-v $PWD/authorized_keys:/var/lib/bastion/authorized_keys:ro`.

* `TRUSTED_USER_CA_KEYS [/full/path/to/file]` - Specifies a file containing public keys of certificate authorities that are trusted to sign user certificates for authentication, or none to not use one. Keys are listed one per line; empty lines and comments starting with `#` are allowed. If a certificate is presented for authentication and has its signing CA key listed in this file, then it may be used for authentication for any user listed in the certificate's principals list. Note that certificates that lack a list of principals will not be permitted for authentication using `TRUSTED_USER_CA_KEYS`. Directive `AuthorizedPrincipalsFile` hardcoded to `/etc/ssh/auth_principals/%u` and in time of build and generated one principals file for presented user - `/etc/ssh/auth_principals/bastion` with the one row `bastion`, and this principal should be listed in the certificate's principals list.

* `GATEWAY_PORTS [true | false]` - Specifies whether remote hosts are allowed to connect to ports forwarded for the client. By default, `sshd` binds remote port forwardings to the loopback address. This prevents other remote hosts from connecting to forwarded ports. `GATEWAY_PORTS` can be used to specify that `sshd` should allow remote port forwardings to bind to non-loopback addresses, thus allowing other hosts to connect. The argument may be `false` to force remote port forwardings to be available to the local host only, `true` to force remote port forwarding to bind to the wildcard address. The default is `false`.

* `PERMIT_TUNNEL [true | false]` - Specifies whether `tun` device forwarding is allowed. The argument must be `true` or `false`. Specifying `true` permits both `point-to-point` (layer 3) and `ethernet` (layer 2). The default is `false`.

* `X11_FORWARDING [true | false]` - Specifies whether `X11` forwarding is permitted. The argument must be `true` or `false`. The default is `false`.

* `TCP_FORWARDING [true | false]` - Specifies whether `TCP` forwarding is permitted. The default is `true`. Note that disabling `TCP` forwarding does not improve security unless users are also denied shell access, as they can always install their own forwarders.

* `AGENT_FORWARDING [true | false]` - Specifies whether `ssh-agent` forwarding is permitted. The default is `true`. Note that disabling agent forwarding does not improve security unless users are also denied shell access, as they can always install their own forwarders.

* `LISTEN_ADDRESS [0.0.0.0]` - Specifies the local addresses should listen on. By default it **0.0.0.0**. Useful when Docker container runs in `Host mode`

* `LISTEN_PORT [22]` - Specifies the port number that listens on. The default is **22**. Useful when Docker container runs in `Host mode`

###  Run Bastion and `expose` port `22222` to outside a host machine

The container assumes your `authorized_keys` file with `644` permissions and mounted under `/var/lib/bastion/authorized_keys`.

#### Docker compose example

```shell
docker-compose up
```
