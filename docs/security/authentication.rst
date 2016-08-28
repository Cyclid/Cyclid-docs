##############
Authentication
##############

********************************
Available authentication methods
********************************

There are three authentication schemes currently supported:

1. HTTP Basic
2. HMAC request signing
3. API token

HTTP Basic
==========

HTTP Basic is self explanitory and works with any standard HTTP client.

HMAC
====

The HMAC scheme uses a shared secret (The ``User.secret`` data) to
generate an `HMAC <https://tools.ietf.org/html/rfc2104>`__ from the request,
with an optional nonce. The header format is:

::

    Authorization: HMAC [user]:[hmac]
    X-HMAC-Nonce: [nonce]

API Token
=========

The Cyclid server can generate a `JWT <https://jwt.io>`__ token for use by simple
non-sensitive clients which don't or can't support HMAC signing. The generated
token should be considered opaque. The header format is:

::

    Authorization: Token [user]:[token]
