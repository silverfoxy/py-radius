Modified the code so that it works with RFC2865 since RFC2138 is now deprecated.
---------------------------------------------------------
RADIUS authentication module for Python 2.6+

(c) 1999 Stuart Bishop <zen@shangri-la.dropbear.id.au>

This module provides basic RADIUS client capabilities, allowing
your Python code to authenticate against any RFC2865 compliant RADIUS
server.


Installation
-----

RPM Package
-----

Usage
-----

The radius.py module can be run from the command line, providing a minimal
RADIUS client to test out RADIUS servers:

    $ python radius.py


The module defines the following items:

    authenticate(username, password, secret, host='127.0.0.1', port=1812, NASip='127.0.0.1', NASport=randint(1, 214748364))

A simple, thread safe function to authenticate off a RADIUS
server with the minimum possible fuss. Returns 1 on success,
0 on failure. May throw a NoResponse or SocketError exception.

    RADIUS(secret, host='radius', port=1645)

Return a new instance of the RADIUS class. RADIUS objects
provide a more efficient interface if your code makes many
calls to the same RADIUS server. RADIUS objects should not
be shared between threads, unless only one thread accesses
the authenticate method at a time.

    NoResponse

Exception thrown if no response or no valid responses are
received.

    SocketError

Subclass of NoResponse. Exception thrown if an exception is
thrown from Python's socket module.

RADIUS instances have the following methods and attributes available:

    authenticate(username, password)

Authenticate a username/password combination. Returns 1 on
success or 0 on failure. May throw a NoResponse or SocketError
exception.

    closesocket()

Close the outgoing UDP socket. Called automatically in the
RADIUS instance's destructor. 

    retries

The number of times the authenticate method tries to 
authenticate before returning a NoResponse exception. Defaults
to 3.

    timeout

The number of seconds the authenticate method waits for
a response from the RADIUS server before giving up and
retrying. Defaults to 5.


Example
-----

    #!/bin/env python
    from getpass import getpass
    from radius import RADIUS

    host = raw_input("Host? (default = '127.0.0.1')")
    port = raw_input('Port? (default = 1812) ')
    NASip = raw_input("NAS IP? (default = '127.0.0.1')")
    NASport = raw_input("NAS Port? (default = random)")
    if not host: host = '127.0.0.1'

    if port: port = int(port)
    else: port = 1812
    
    if not NASip: NASip = '127.0.0.1'

    if NASport: NASport = int(NASport)
    else:  NASport = randint(1,2147483647)

    secret = ''
    while not secret: secret = getpass('RADIUS Secret? ')

    r = RADIUS(secret,host,port)

    uname,passwd = None,None

    while not uname:  uname = raw_input("Username? ")
    while not passwd: passwd = getpass("Password? ")

    if r.authenticate(uname,passwd,NASip,NASport):
        print "Authentication Succeeded"
    else:
        print "Authentication Failed
