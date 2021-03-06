## tsproxy: A transparent HTTP to SOCKS proxy ##

### What it does ###
In short, it accepts redirected HTTP requests, and either completes them itself or redirects them via a SOCKS proxy. 

My original use-case for creating this was a bit remote, but I have since found this to have a 
number of other uses, such as redirecting only certain sites via TOR or a specific-country proxy.
You may find many other uses. :)

### Features ###
- Does not need to be run as root
- One thread per listening socket and connection
- Supports keep-alive, pipelining, and anything else a client could want to do over HTTP
- Supports multiple upstream proxies with pattern matching on hostname to decide which to use
- Supports SOCKS4, SOCKS4a, and SOCKS5 proxies with no authentication
- Supports HTTPS, as much as a transparent proxy can

### Usage ###
- Compile with "make". The SSL binaries will fail to compile without GnuTLS installed, but the normal should be fine.
- Edit the config file (sample is provided)
- Run the version you want. The "s" suffix supports SSL, the "d" suffix daemonizes.
- Redirect packets with something like: iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to 8888
- You can redirect packets from the OUTPUT chain too, but be careful not to cause an infinite loop
- While running, a SIGINT (Ctrl-C) or SIGTERM (kill) once will start a clean shutdown. A second will exit immediately

### Supported Platforms ###
While tsproxy has only been tested on Linux x86 and x64, it should theoretically work on almost any POSIX system
with a C compiler, including Cygwin. Platforms without iptables will have to find another way to redirect packets,
though they could be redirected from another system. Some editing of the Makefile may be required. 

### HTTPS ###
HTTPS is supported via GnuTLS, but is a bit ugly. The protocol is designed to defeat man-in-the-middle attacks,
and that is effectively what a transparent proxy does. 

To use HTTPS, you will need a X.509 key and certificate with CA capability. Generating your own will work,
but you will have to install the certificate into every client's trust list. 

Every time a HTTPS connection is intercepted, the software will use the TLS server name indicator to generate
an appropriate certificate on-the-fly and present it to the client for the connection. If this feature is not
supported by your client, it will present a * certificate instead.

### License ###
tsproxy is licensed under GPLv2. Should someone wish to use it as part of a closed-source product,
feel free to contact me.

