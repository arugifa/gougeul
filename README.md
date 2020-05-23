# Kloügle - Freedom from the Cloud

[![Build Status](https://travis-ci.org/arugifa/klougle.svg?branch=master)](https://travis-ci.org/arugifa/klougle)

**Kloügle** offers you to host, on your own server, a range of services
traditionally provided by web companies like **Google**.

If you care about your privacy, and think that nobody else should be able to
access to your very own secrets, then Kloügle is made for you!


## Overview

Currently, here is what you can expect to find in Kloügle:

Application | Replacement for | Notes
----------- | --------------- | -----
**Email Aliasing**\*\*\* ([SimpleLogin](https://simplelogin.io/)) | [Gmail](https://mail.google.com/) + [Mailvelope](https://www.mailvelope.com/) | Must be used with a secure email provider, like [ProtonMail](https://protonmail.com/)
**Finance Manager** ([Firefly](https://www.firefly-iii.org/)) | | |
**News Reader** ([Miniflux](https://miniflux.app/))| [Google News](https://news.google.com/), [Feedly](https://feedly.com/) |
**Notes**\* ([Standard Notes](https://standardnotes.org/)) | [Google Keep](https://keep.google.com/), [Evernote](https://evernote.com/) |
**Task Board** ([Kanboard](https://kanboard.org/)) | [Trello](https://trello.com/) |
**Web Library** ([Wallabag](https://wallabag.org/)) | [Pocket](https://getpocket.com/) |

\* application also available on smartphone,
\*\* browser add-ons available

The following services will also become available during the next few months:

- a **file storage and synchronization** service,
  like [Google Drive](https://drive.google.com/) or [Dropbox](https://www.dropbox.com/),
- a **calendar**, like [Google Calendar](https://calendar.google.com/),
- a **contact** management tool, like [Google Contacts](https://contacts.google.com/).

It is not my goal to reinvent the wheel: by carefully selecting a set of
open-source softwares, Kloügle is able to provide you the simplest and most
user-friendly alternatives to proprietary services.

If you are looking for even more services not yet supported by Kloügle,
have a look to [Framasoft](https://degooglisons-internet.org/en/list/). This
french association, which believes that you should keep the control over your
personal data, provides for free a wide range of Google-like services 😉


### How Kloügle is different from other self-hosted Cloud solutions?

People shouldn't have to follow any tutorial, in order to know how to deploy
a web service. This knowledge should be contained into some
[Infrastructure as Code](https://en.wikipedia.org/wiki/Infrastructure_as_code)
recipes, that everybody would just have to run. Nothing more.

For this purpose, Kloügle does not provide any fancy administration dashboard,
neither a market place. In fact, Kloügle audience is composed of hackers, who
like to keep control over their infrastructure by just editing a set of files.
This makes the whole thing easier to automate, and adapt to everyone need.

Several manual steps still have to be performed, in order to deploy Kloügle,
depending on your running environment. But the goal, over the long-term, is to
make Kloügle the de-facto easy to use, extend, customize and deploy self-hosted
Cloud solution™.


## Getting Started

### Requirements

#### Software Dependencies

Kloügle depends on [Terraform](https://www.terraform.io/) to set-up your server.
This great tool makes possible to deploy Kloügle in many different environments.


##### On Linux

Unfortunately, **Terraform** is not yet available on most **Linux** distributions.
If it's your case, you have to install it manually:

```sh
# First, download Terraform.
wget https://releases.hashicorp.com/terraform/0.12.24/terraform_0.12.24_linux_amd64.zip
unzip terraform_0.12.24_linux_amd64.zip -d /tmp

# Then, install Terraform on your system.
sudo mv /tmp/terraform /usr/local/bin/
```


##### On macOS

Lucky you: if you already use [Homebrew](https://brew.sh/) to manage your packages,
then just type:

```sh
brew install terraform
```

If you don't use Homebew yet, well... you really should 😋


#### DNS Records

Every service deployed with Kloügle is running on its own sub-domain. Which
means that, you not only have to buy a domain name, but also create address
records for every Kloügle service.

As only a very few services are available for now, it's pretty simple:

- `alias.<YOUR_DOMAIN>`
- `finance.<YOUR_DOMAIN>`
- `library.<YOUR_DOMAIN>`
- `mail.<YOUR_DOMAIN>`
- `news.<YOUR_DOMAIN>`
- `notes.<YOUR_DOMAIN>`
- `tasks.<YOUR_DOMAIN>`

When deploying Kloügle online, **Terraform** will provide you the IP address of
your Kloügle server. So you can update your DNS zone afterwards.

However, if you only want to play with Kloügle locally, then the simplest option
is to update the `/etc/hosts` file of your machine. For example:

```ini
127.0.0.1    alias.localhost finance.localhost library.localhost mail.localhost news.localhost notes.localhost sync.notes.localhost tasks.localhost
```


### Installing Kloügle

Kloügle is meant to be deployable in many different locations, like cloud
providers or physical servers.

But whatever is your running environment, you first need to download Kloügle on
your local machine:

```sh
git clone https://github.com/arugifa/klougle.git
cd klougle/
```


#### Locally

When deploying Kloügle locally, you also need to have **Docker** installed.
For Linux systems, please refer to the documentation of your distribution.
For macOS, with **Homebrew**, you can just type: `brew cask install docker`

Then, deploy Kloügle services with **Terraform**:

```sh
cd docker/
terraform init
terraform apply
```

And that's it!

N.B.: please note there is no SSL support when deploying Kloügle locally.


#### On Bare-Metal Servers

When deploying Kloügle remotely, **Terraform** is connecting to the **Docker**
daemon with TLS, for security reasons. That's why you first need to manually
configure the Docker daemon socket to use TLS.

Please refer to the official [Docker guide](https://docs.docker.com/engine/security/https/)
for more insights. After what, copy the `ca.pem`, `server-cert.pem` and `server-key.pem`
files into `~/.docker/klougle` on your local machine.

Also, you have to choose a **F**ully **Q**ualified **D**omain **N**ame for your server
(if you don't have one yet), update your DNS zone, and finally deploy Kloügle as follows:

```sh
cd docker/
terraform init
terraform apply -var 'host=<SERVER_FQDN>' -var 'letsencrypt_email=<EMAIL_ADDRESS>'
```


#### On Cloud Providers

When deployed in the cloud, Kloügle is running inside a virtual machine on
[RancherOS](https://rancher.com/rancher-os/), a minimalist Docker based distribution.

Each cloud provider has different requirements. But for all of them, you will
have to choose a **F**ully **Q**ualified **D**omain **N**ame for your virtual machine.
For example: `cloud.<YOUR_DOMAIN>`

Then, after Kloügle deployment, connecting to your Kloügle server will be as easy as:

```sh
ssh rancher@<SERVER_FQDN>
```

Only [OpenStack](https://www.openstack.org/) providers are supported for now.
The reason is very simple: I am currently the only user of Kloügle, and worked
in the past for a company operating an OpenStack cloud 🤠


##### OpenStack

**Requirements:**

- your **OpenStack RC file**, that you can download from the OpenStack dashboard,
- the **flavor** name you want to use for the server: 1 vCPU and 2048 MB of RAM
  are largely enough,
- all the **key pairs** you want to use to connect to the Kloügle virtual machine:
  the pair associated to the machine where you are running **Terraform** is the
  most important one, of course.

Regarding the network configuration, many scenarios exist.

<a href="http://media.figura.live/klougle/openstack/topology_floating_ip.png">
    <img alt="Kloügle server with floating IP" width="400" src="http://media.figura.live/klougle/openstack/topology_floating_ip.png">
</a>

If your provider lets you accessing to your virtual machines from Internet via
floating IPs, then you have to manually create:

- an **internal network**, to which Kloügle server will connect,
- a **router**, acting as a gateway to the external network (made available by
  your provider).

<a href="http://media.figura.live/klougle/openstack/topology_external_interface.png">
    <img alt="Kloügle server with external interface" width="400" src="http://media.figura.live/klougle/openstack/topology_external_interface.png">
</a>

If your provider allows you instead to attach an interface directly to the
external network, then you don't have anything to do! 🙂

**Deployment:**

```sh
# First, set-up your OpenStack and SSH environment.
. <OPENRC_FILE>
ssh-add

# Then, deploy your Kloügle server.
cd openstack/
terraform init

# If you use a floating IP pool:
terraform apply -var 'flavor=<FLAVOR_NAME>' -var 'key_pairs=["<PAIR_1>","<PAIR_2>"]' -var 'fqdn=<SERVER_FQDN>' -var 'floating_ip_pool=<POOL_NAME>' -var 'internal_network=<NETWORK_NAME>'

# If you can instead directly connect to the external network:
terraform apply -var 'flavor=<FLAVOR_NAME>' -var 'key_pairs=["<PAIR_1>","<PAIR_2>"]' -var 'fqdn=<SERVER_FQDN>' -var 'external_network=<NETWORK_NAME>'

# Wait a couple of minutes for the server to be up and running, and copy the public IP
# displayed by Terraform. You can now manually assign it to the server FQDN,
# in the DNS zone of your domain.

# After having deployed the Kloügle server,
# it is finally time for deploying Kloügle services with Docker.
cd ../docker/
terraform init
terraform apply -var 'host=<SERVER_FQDN>' -var 'letsencrypt_email=<EMAIL_ADDRESS>'
```

### Post-Deployment Actions

In order to be able to send/receive emails from your new Cloud, you need to
add a few more records to your DNS zone.

But first, retrieve your very own personal
[DKIM](https://en.wikipedia.org/wiki/DomainKeys_Identified_Mail) public key:

```sh
CLOUD_FQDN=cloud.<YOUR_DOMAIN>

docker --tls --tlscacert ~/.docker/klougle/ca.pem --tlscert ~/.docker/klougle/cert.pem --tlskey ~/.docker/klougle/key.pem -H $CLOUD_FQDN:2376 exec simplelogin cat /sl/dkim/dkim.pub.key
```

Then, add these new records to your DNS zone:

Type | Domain | Target | Description
---- | ------ | ------ | -----------
**DKIM**  | `dkim._domainkey.alias.<YOUR_DOMAIN>.` | `v=DKIM1;k=rsa;p=<DKIM_PUBLIC_KEY>;` | Expose your DKIM public key to the world.
**MX**    | `alias.<YOUR_DOMAIN>.` | `mail.<YOUR_DOMAIN>.` | Email server for your aliases.
**SPF**   | `alias.<YOUR_DOMAIN>.` | `v=spf1 mx -all` | Only your email server can send emails coming from `@alias.<YOUR_DOMAIN>`
**DMARC** | `_dmarc.alias.<YOUR_DOMAIN>.` | `v=DMARC1;p=quarantine;` | Ask the world to mark as SPAM emails coming from your domain, but not sent from your email server.

After having done that, you can go on [MxToolbox.com](https://mxtoolbox.com/)
to check you properly configured your DNS records for `alias.<YOUR_DOMAIN>`.
You should especially check that the IP address of your server is not
blacklisted by global email providers. Otherwise, you will never receive emails
forwarded from your Cloud 👎


### SSL Troubleshooting

SSL certificates are automatically generated with Let's Encrypt when deploying
Kloügle. However, some problems can appear during this process, as described
below.


#### CAA Requests

You must host your DNS zone on a provider [answering correctly](https://sslmate.com/caa/support)
to [CAA requests](https://letsencrypt.org/docs/caa/). If not, then you have
unfortunately no choice but to migrate your zone to another provider...


#### Rate Limits

**Kloügle currenly has a known issue regarding SSL certificates generation and
renewal. See [there](https://github.com/arugifa/klougle/issues/23) for more info.**

If your browser keeps saying your SSL certificates are invalid when connecting
to Kloügle services, you should probably wait first a couple of minutes, and
give time to Kloügle to request certificates to [Let's Encrypt](https://letsencrypt.org/).

However, if the problem persists, it probably means that you have hit Let's
Encrypt [rate limits](https://letsencrypt.org/docs/rate-limits/).

This can happen when your Cloud provider doesn't assign you an unique external
IP, but instead gives you one which is shared among many customers.

You can observe this problem by yourself, just by looking inside the reverse
proxy's logs:

```sh
$ ssh rancher@<SERVER_FQDN>  # If you run Kloügle on a remote virtual machine

$ docker logs -f traefik
```

You better wait for the next successful attempt to generate SSL certificates,
which should happen at some point...


## Configuring Kloügle

You need to perform a couple of actions after having installed Kloügle:

- create new users or change default passwords,
- configure some services via their admin interface.


### Default Passwords

As Kloügle doesn't provide any central authentication system for the moment:

- every time you want to change your password, you have to do it for all services,
- there is no "Forgot Password" feature yet available, so keep your password(s) safe...

For the following services, you have to create a new user by yourself:

- **email aliasing:** `https://alias.<YOUR_DOMAIN>/auth/register`
- **finance manager:** `https://finance.<YOUR_DOMAIN>/`
- **notes:** `https://notes.<YOUR_DOMAIN>/` (please update the server's
  URL when trying to register/sign in with `https://sync.notes.<YOUR_DOMAIN>/`;
  by default, a fake one is used to prevent anyone to create accounts on your
  setup)

But for the following ones, here are the default credentials:

- **web library:** `wallabag` / `wallabag` (`https://library.<YOUR_DOMAIN>/config#set4`)
- **news reader:** `admin` / `password` (`https://news.<YOUR_DOMAIN>/settings`)
- **task board:** `admin` / `admin` (`https://tasks.<YOUR_DOMAIN>/user/1/password`)


### Additional Configuration

A couple of services need to be configured after Kloügle installation.


#### Email Aliasing

**If you didn't receive an email to activate your email aliasing account, it
means you either didn't configure properly your DNS zone, or your server's IP
address is blacklisted by your email provider. You should fix that first before
creating aliases and registering with them on other websites. You can use
[MxToolbox.com](https://mxtoolbox.com/) to help you troubleshooting this
problem.**

Make your account premium to have unlimited aliases:

```sh
CLOUD_FQDN=cloud.<YOUR_DOMAIN>
ALIAS_ACCOUNT=<SIMPLELOGIN_ACCOUNT_EMAIL>

docker --tls --tlscacert ~/.docker/klougle/ca.pem --tlscert ~/.docker/klougle/cert.pem --tlskey ~/.docker/klougle/key.pem -H $CLOUD_FQDN:2376 exec simplelogin_db psql -U simplelogin -c "UPDATE users SET lifetime=TRUE WHERE email='$ALIAS_ACCOUNT';"
```

#### Web Library

You should:

- enable asynchronous imports with Redis (`https://library.<YOUR_DOMAIN>/settings#set-import`)
- enable local download of article images (`https://library.<YOUR_DOMAIN>/settings#set-misc`)


## Contributing

If you want Kloügle to support your favorite platform, feel free to ask for it
by [opening an issue](https://github.com/arugifa/klougle/issues/new), or even better,
by [proposing a pull request](https://github.com/arugifa/klougle/compare).
