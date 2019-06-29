# cisco anyconnect server docker

## Sectoin I: Introduction

As an Iranian Citizen I always have trouble with cencorship forced by our beloved governments on one hand and foreign sanctoins burden on another hand. They banned our IP, filtered our domain, drop MTProto and OpenVPN protocol or even removed our servers from their data centers, but we are always one step ahead :-)

To address these issues I start to use docker to ease pain of installing and installing and installing same applications again and again and again. In this way I can change my host and deploy my applications on new host in less than a minute. One of my favorit application to bypass filter is cisco anyconnect. I like it because it relay traffic between client and server like OpenSSH and HTTPS and government can't discriminate between Anyconnect traffic and HTTPS as a result they can't block anyconnect traffic unless they block all HTTPS traffic.

There exist two way of client authentication, with password or certificate. Password authentication is straightforward and it doesn't need complex configuration but configuration complexity of certificate authentication is rather high; You should understand how certificate works and generate certificate for every user. On the other hand client get rid of typing password every time they want to connect to network. I tried to solve issue of configuration by introducing `gcc` **(generate client certificate)** command.  You can easily generate new certificate by typing `gcc <username>` (more on that on section III).

In the section II I will show password authentication configuration and how you can add new client. In section III I will use same procedure to introduce certificate authentication. Some tricks to boost your network speed and delay provided in final section.

## Section II: password authentication config
Easiest ocserv configuration is its password authentication. By passing some argument at build time you can build your own docker image customized for your domain. Run below command in root directory of this project to build fresh ocserve docker image with password authentication customized for your domain. Meaning of build arguments provided in Table. 1 .

```bash
$ docker build --build-arg ORGANIZATION="Example Corp" --build-arg DOMAIN=example.com -t anyconnect:password ./password/
```

After successful build run anyconnect image by:

```bash
$ docker run --name any-pass -it --privileged -p 4321:4321 anyconnect:password
Parsing plain auth method subconfig using legacy format
note: setting 'plain' as primary authentication method
note: setting 'file' as supplemental config option
root@a86f00e3e939:/etc/ocserv#
``` 

You will directed to **any-pass** container bash. Create clients with:

```bash
root@a86f00e3e939:/etc/ocserv# ocpasswd -c /etc/ocserv/ocpasswd <username>
```

After setting up users and their password then hit <kbd>Ctrl-p</kbd> <kbd>Ctrl-q</kbd> to detach from bash. To add more users in future, first execute new bash on **any-pass** with:

```bash
$ docker exec -it anyconnect /bin/bash
root@a86f00e3e939:/etc/ocserv#
```

and then create users as stated before and quit with `exit`.

<br>

|      ARG     |                   Meaning                  |     Default    |
|:------------:|:------------------------------------------:|:--------------:|
| ORGANIZATION | Organization used to generate cetificates. | "Example Corp" |
|    DOMAIN    | Certificate will generate for this domain  | "example.com"  |

*Table. 1: meaning of password configuration build args*

## Section III: certificate authentication config

### References

[1] : [https://lowendbox.com/blog/install-openconnect-server-on-ubuntu-16-04/](https://lowendbox.com/blog/install-openconnect-server-on-ubuntu-16-04/)<br/>
[2] : [https://www.linuxbabe.com/ubuntu/openconnect-vpn-server-ocserv-ubuntu-16-04-17-10-lets-encrypt](https://www.linuxbabe.com/ubuntu/openconnect-vpn-server-ocserv-ubuntu-16-04-17-10-lets-encrypt)<br/>
[3] : [https://www.linuxbabe.com/ubuntu/certificate-authentication-openconnect-vpn-server-ocserv](https://www.linuxbabe.com/ubuntu/certificate-authentication-openconnect-vpn-server-ocserv)
