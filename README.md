<h1 align="center">Go ICAP Server</h1>
<p align="center">
    <em> k8-go-icap server.</em>
</p>

<p align="center">
    <a href="https://github.com/k8-proxy/go-icap-server/actions/workflows/build.yml">
        <img src="https://github.com/k8-proxy/go-icap-server/actions/workflows/build.yml/badge.svg"/>
    </a>
    <a href="https://codecov.io/gh/k8-proxy/go-icap-server">
        <img src="https://codecov.io/gh/k8-proxy/go-icap-server/branch/main/graph/badge.svg"/>
    </a>	    
    <a href="https://goreportcard.com/report/github.com/k8-proxy/go-icap-server">
      <img src="https://goreportcard.com/badge/k8-proxy/go-icap-server" alt="Go Report Card">
    </a>
	<a href="https://github.com/k8-proxy/go-icap-server/pulls">
        <img src="https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat" alt="Contributions welcome">
    </a>
    <a href="https://opensource.org/licenses/Apache-2.0">
        <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg" alt="Apache License, Version 2.0">
    </a>
</p>

# ICAPeg

Open Source multi-vendor ICAP server

Scan files requested via a proxy server using ICAPeg ICAP server, ICAPeg is an ICAP server connecting web proxies with API based scanning services and more soon!. ICAPeg currently supports [Glasswall]() , [VirusTotal](https://www.virustotal.com/gui/home/upload),[VMRAY](https://www.vmray.com/) , [MetaDefender](https://metadefender.opswat.com/?lang=en) & [Clamav](https://www.clamav.net/)  for scanning the files following the ICAP protocol. If you don't know about the ICAP protocol, here is a bit about it:

## What is ICAP?

**ICAP** stands for **Internet Content Adaptation Protocol**. If a **content** (for example: file) you've requested over the internet
to download or whatever, needs **adaptation**(some kind of modification or analysis), the proxy server sends the content to the ICAP server for adaptation and after performing the required tasks on the content, the ICAP server sends it back to the proxy server so that it may return the adapted content back to the destination. This can occur both during request and response.

To know more about the ICAP protocol, [check this out](https://tools.ietf.org/html/rfc3507).

## Things to have

Before starting to play with ICAPeg, make sure you have the following things in your machine:

1. **Golang**(latest enough to be able to use go mod)

     ***A sample installation of go version 1.14***:

     Prepare the apt packages    
  ```bash
    sudo apt update

  ```

  ```bash
    sudo apt upgrade

  ```

Link of download of version 1.14
    https://dl.google.com/go/go1.14.linux-amd64.tar.gz

Use the command
  ```bash
    wget https://dl.google.com/go/go1.14.linux-amd64.tar.gz

  ```
Untar in /usr/local

  ```bash
    tar -C /usr/local -xzf go1.14.linux-amd64.tar.gz

  ```

Add /usr/local/go/bin to the PATH environment variable:

  ```bash
    export PATH=$PATH:/usr/local/go/bin

  ```
> Note: this command adds the PATH temporarily to the environment variables, the path is removed if SSH is broken, or system reboots.


2. A **proxy** server

squid is an example in this readme

3. A scanner vendor. `ICAPeg` now supports `VirusTotal`,  `MetaDefender` , `VMRay` & `Clamav` as scanner vendors.
Make sure that you setup your scanner vendor properly. You can setup your scanners for both [RESPMOD](https://tools.ietf.org/html/rfc3507#page-27) & [REQMOD](https://tools.ietf.org/html/rfc3507#page-23).  Although, not every scanner supports every mods. [Check this out](MODS.md) to know which scanner supports which mods.

Setup Glasswall :**

Insert `Glasswall` as your scanner vendor in the config.toml file

  ```code
    resp_scanner_vendor = "glasswall"
  ```

  Or,

  ```code
    req_scanner_vendor = "glasswall"
  ```

Setup **VirusTotal:**

Insert `VirusTotal` as your scanner vendor in the config.toml file

  ```code
    resp_scanner_vendor = "virustotal"
  ```

  Or,

  ```code
    req_scanner_vendor = "virustotal"
  ```

In that same file, add a **VirusTotal API key** in the `api_key` field of the `[virustotal]` section. [Here is how you can get it](VIRUSTOTALAPI.md).

Setup **MetaDefender:**

Insert `MetaDefender` as your scanner vendor in the config.toml file

  ```code
    resp_scanner_vendor = "metadefender"
  ```

  Or,

  ```code
    req_scanner_vendor = "metadefender"
  ```

In that same file, add a **MetaDefender API key** in the `api_key` field of the `[metadefender]` section. [Here is how you can get it](METADEFENDER.md).

Setup **VMRay:**

Insert `vmray` as your scanner vendor in the config.toml file

  ```code
    resp_scanner_vendor = "vmray"
  ```

  Or,

  ```code
    req_scanner_vendor = "vmray"
  ```

In that same file, add a **VMRay API key** in the `api_key` field of the `[vmray]` section. [Get your api key by requesting a free trial](https://www.vmray.com/analyzer-malware-sandbox-free-trial/).

Setup **Clamav:**

Insert `clamav` as your scanner vendor in the config.toml file

  ```code
    resp_scanner_vendor = "clamav"
  ```

Next, provide the **clamd socket file path**(getting back to this in a bit) in the config.toml file inside the clamav section

  ```code
    socket_path = "<path to clamd socket file>"
  ```

[Here is how you setup clamav and generate the socket file](CLAMAVSETUP.md)


**NOTE**: All the settings of ICAPeg is present in the **config.toml** file in the repo. Also before selecting your vendors as the scanners, keep in mind to check whether that certain vendor supports the modification mode or not. For example, when adding ``virustotal``  as the ``resp_scanner_vendor``, check under the configuration of ``virustotal`` if the ``resp_supported`` flag is true or not. Likewise for ``req_scanner_vendor`` and for any other vendors. Also you can provide `none` in the ``resp/req_scanner_vendor/vendor_shadow`` fields to indicate no vendor is provided & ICAPeg is just gonna avoid processing the requests.

## How do I turn this thing on!!

To turn on the ICAPeg server, proceed with the following steps (assuming you have golang installed in you system):

1. Clone the ICAPeg repository

  ```bash
    git clone https://github.com/egirna/icapeg.git

  ```


2. Enable `go mod`

  ```bash
    export GO114MODULE=on

  ```
>    In case not using go version 1.14, you could discover your version

  ```bash
    go version

  ```

>           You should use the corresponding export command

>           1.14 ===> export GO114MODULE=on

>           1.13 ===> export GO113MODULE=on

>           etc.

3.  Change the directory to the repository

  ```bash
    cd icapeg/
  ```

4. Add the dependencies in the vendor file

  ```bash
    go mod vendor
  ```

5. Build the ICAPeg binary by

  ```bash
    go build .
  ```

6. Finally execute the file like you would for any other executable according to your OS, for Unix-based users though

  ```bash
    ./icapeg
  ```

   You should see something like, ```ICAP server is running on localhost:1344 ...```. This tells you the ICAP server is up and running
OR, you can do none of the above and simply execute the **run.sh** shell file provided, by

  ```bash
   ./run.sh
  ```
That should do the trick.

2. Now that the server is up and running, the next thing to do is setup a proxy server which can send the request body to the ICAPeg server for adaptation. [Squid](http://www.squid-cache.org/) looks like just the thing for the job, go to the site provided and set it up like you want.
After setting up your proxy server for example squid, change its configuration file:

Open squid.conf file

  ```bash
    sudo nano /etc/squid/squid.conf
  ```
Add the following lines at the bottom of your ACLs configurations

  ```configuration
    icap_enable on
    icap_service service_resp respmod_precache icap://127.0.0.1:1344/respmod
    adaptation_access service_resp allow all
  ```

Add the following line at the end of the file

  ```configuration
    cache deny all
  ```

A sample conf file for squid exists in the repository in a file
   [squid.conf](https://github.com/mkaram007/icapeg/blob/fa4ce337b27a2583c93c5dc81d8c7310fdc38e3a/squid.conf)


Save and close the file
  Press CTRL + x, then press Y, then Enter

Restart squid:

  ```bash
    systemctl restart squid
  ```

## Things to keep in mind

1. You will have to restart the ICAP server each time you change anything in the config file.

2. You will have to restart squid whenever you restart the ICAP.

3. You need to configure your network(or your browser)'s proxy settings to go through squid.

## More on ICAPeg

1. [Remote ICAP Servers & Shadowing](REMOTEANDSHADOW.md)

2. [Logging](LOGS.md)


### Contributing

This project is still a WIP. So you can contribute as well. See the contributions guide [here](CONTRIBUTING.md).

### License

ICAPeg is licensed under the [Apache License 2.0](LICENSE).
