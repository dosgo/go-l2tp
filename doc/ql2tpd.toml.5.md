% ql2tpd.toml(5) go-l2tp _VERSION_ | go-l2tp
% Katalix Systems, Ltd
% _DATE_

# NAME

**ql2tpd.toml** - configuration file for **ql2tpd**

# DESCRIPTION

The **ql2tpd.toml** file configures **ql2tpd**.  It calls out the L2TP tunnels and sessions to establish.

**ql2tpd.toml** is written in the TOML markup langange (https://toml.io/en/).

Tunnel and session instances are called out in the configuration file using named TOML tables.

Each tunnel or session instance table contains configuration parameters for that instance as key:value pairs.

Each tunnel and session has a minimal set of configuration which ***must*** be specified.

In addition, each tunnel or session entry may call out various optional key:value pairs which will control **ql2tpd**'s runtime behaviour.

These options are generally not required, and **ql2tpd** will use sensible defaults for them if they are not included in the configuration.

## TUNNEL CONFIGURATION

Tunnels are described using named entries in the 'tunnel' table.

Each tunnel entry describes a single tunnel instance, and must call out at least:

* the tunnel L2TP version (only L2TPv3 is supported),
* the tunnels encapsulation protocol,
* the local IP address,
* the local tunnel ID,
* the peer's IP address,
* the peer's tunnel ID.

Here is the full list of tunnel configuration options:

	# This is a tunnel instance named "t1"
	[tunnel.t1]

	# version specifies the version of the L2TP specification the
	# tunnel should use.
	# Only "l2tpv3" is supported.
	version = "l2tpv3"

	# encap specifies the encapsulation to be used for the tunnel.
	# L2TPv3 tunnels may be UDP or IP.
	encap = "udp"

	# local specifies the local address that the tunnel should
	# bind its socket to
	local = "127.0.0.1:5000"

	# tid specifies the local tunnel ID of the tunnel.
	# Tunnel IDs must be unique for the host.
	# L2TPv2 tunnel IDs are 16 bit, and may be in the range 1 - 65535.
	# L2TPv3 tunnel IDs are 32 bit, and may be in the range 1 - 4294967295.
	tid = 62719

	# peer specifies the address of the peer that the tunnel should
	# connect its socket to
	peer = "127.0.0.1:5001"

	# ptid specifies the peer's tunnel ID for the tunnel.
	# The peer's tunnel ID must be unique for the peer, and are unrelated
	# to the local tunnel ID.
	# The rules for tunnel ID range apply to the peer tunnel ID too.
	ptid = 72819

	# hello_timeout if set enables L2TP keep-alive (HELLO) messages.
	# A hello message is sent N milliseconds after the last control
	# message was sent or received.  It allows for early detection of
	# tunnel failure on quiet connections.
	# By default no keep-alive messages are sent.
	hello_timeout = 7500 # milliseconds

## SESSION CONFIGURATION

Sessions are described using named entries in the 'session' table inside the parent tunnel table.

Each session entry describes a single session instance within the parent tunnel, and must call out at least:

* the pseudowire type to be used (this must be Ethernet),
* the local session ID,
* the peer's session ID

Here is the full list of session configuration options:

	# This is a session instance called "s1" within parent tunnel "t1".
	# Session instances are always created inside a parent tunnel.
	[tunnel.t1.session.s1]

	# pseudowire specifies the type of layer 2 frames carried by the session.
    # Static sessions support Ethernet pseudowires only.
	pseudowire = "eth"

	# sid specifies the local session ID of the session.
	# Session IDs must be unique to the tunnel for L2TPv2, or unique to
	# the peer for L2TPv3.
	# L2TPv2 session IDs are 16 bit, and may be in the range 1 - 65535.
	# L2TPv3 session IDs are 32 bit, and may be in the range 1 - 4294967295.
	sid = 12389

	# psid specifies the peer's session ID for the session.
	# The peer's session ID is unrelated to the local session ID.
	# The rules for the session ID range apply to the peer session ID too.
	psid = 1234

	# seqnum, if set, enables the transmission of sequence numbers with
	# L2TP data messages.  Use of sequence numbers enables the data plane
	# to reorder data packets to ensure they are delivered in sequence.
	# By default sequence numbers are not used.
	seqnum = false

	# cookie, if set, specifies the local L2TPv3 cookie for the session.
	# Cookies are a data verification mechanism intended to allow misdirected
	# data packets to be detected and rejected.
	# Transmitted data packets will include the local cookie in their header.
	# Cookies may be either 4 or 8 bytes long, and contain aribrary data.
	# By default no local cookie is set.
	cookie = [ 0x12, 0xe9, 0x54, 0x0f, 0xe2, 0x68, 0x72, 0xbc ]

	# peer_cookie, if set, specifies the L2TPv3 cookie the peer will send in
	# the header of its data messages.
	# Messages received without the peer's cookie (or with the wrong cookie)
	# will be rejected.
	# By default no peer cookie is set.
	peer_cookie = [ 0x74, 0x2e, 0x28, 0xa8 ]

	# interface_name, if set, specifies the network interface name to be
	# used for the session instance.
	# By default the Linux kernel autogenerates an interface name specific to
	# the pseudowire type, e.g. "l2tpeth0", "ppp0".
	# Setting the interface name can be useful when you need to be certain
	# of the interface name a given session will use.
	# By default the kernel autogenerates an interface name.
	interface_name = "l2tpeth42"

	# l2spec_type specifies the L2TPv3 Layer 2 specific sublayer field to
	# be used in data packet headers as per RFC3931 section 3.2.2.
	# Currently supported values are "none" and "default".
	# By default no Layer 2 specific sublayer is used.
	l2spec_type = "default"

# SEE ALSO

**ql2tpd**(1)
