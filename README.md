# Pure-Python ECDSA

[![build status](https://travis-ci.org/warner/python-ecdsa.png)](http://travis-ci.org/warner/python-ecdsa)
[![Coverage Status](https://coveralls.io/repos/warner/python-ecdsa/badge.svg)](https://coveralls.io/r/warner/python-ecdsa)
[![condition coverage](https://img.shields.io/badge/condition%20coverage-84%25-yellow)](https://travis-ci.org/warner/python-ecdsa/jobs/606048635#L711)
[![Latest Version](https://img.shields.io/pypi/v/ecdsa.svg?style=flat)](https://pypi.python.org/pypi/ecdsa/)


This is an easy-to-use implementation of ECDSA cryptography (Elliptic Curve
Digital Signature Algorithm), implemented purely in Python, released under
the MIT license. With this library, you can quickly create keypairs (signing
key and verifying key), sign messages, and verify the signatures. The keys
and signatures are very short, making them easy to handle and incorporate
into other protocols.

## Features

This library provides key generation, signing, and verifying, for five
popular NIST "Suite B" GF(p) (_prime field_) curves, with key lengths of 192,
224, 256, 384, and 521 bits. The "short names" for these curves, as known by
the OpenSSL tool (`openssl ecparam -list_curves`), are: `prime192v1`,
`secp224r1`, `prime256v1`, `secp384r1`, and `secp521r1`. It includes the
256-bit curve `secp256k1` used by Bitcoin. There is also support for the
regular (non-twisted) variants of Brainpool curves from 160 to 512 bits. The
"short names" of those curves are: `brainpoolP160r1`, `brainpoolP192r1`,
`brainpoolP224r1`, `brainpoolP256r1`, `brainpoolP320r1`, `brainpoolP384r1`,
`brainpoolP512r1`.
No other curves are included, but it is not too hard to add support for more
curves over prime fields.

## Dependencies

This library uses only Python and the 'six' package. It is compatible with
Python 2.6, 2.7 and 3.3+. It also supports execution on the alternative
implementations like pypy and pypy3.

If `gmpy2` or `gmpy` is installed, they will be used for faster arithmetic.
Either of them can be installed after this library is installed,
`python-ecdsa` will detect their presence on start-up and use them
automatically.

To run the OpenSSL compatibility tests, the 'openssl' tool must be in your
`PATH`. This release has been tested successfully against OpenSSL 0.9.8o,
1.0.0a, 1.0.2f and 1.1.1d (among others).


## Installation

This library is available on PyPI, it's recommended to install it using `pip`:

```
pip install ecdsa
```

In case higher performance is wanted and using native code is not a problem,
it's possible to specify installation together with `gmpy2`:

```
pip install ecdsa[gmpy2]
```

or (slower, legacy option):
```
pip install ecdsa[gmpy]
```

## Speed

The following table shows how long this library takes to generate keypairs
(`keygen`), to sign data (`sign`), and to verify those signatures (`verify`).
All those values are in seconds.
For convenience, the inverses of those values are also provided:
how many keys per second can be generated (`keygen/s`), how many signatures
can be made per second (`sign/s`) and how many signatures can be verified
per second (`verify/s`). The size of raw signature (generally the smallest
way a signature can be encoded) is also provided in the `siglen` column.
Use `tox -e speed` to generate this table on your own computer.
On an Intel Core i7 4790K @ 4.0GHz I'm getting the following performance:

```
                  siglen    keygen   keygen/s      sign     sign/s    verify   verify/s
        NIST192p:     48   0.00033s   3062.43   0.00036s   2748.08   0.00062s   1605.66
        NIST224p:     56   0.00041s   2424.07   0.00046s   2196.71   0.00083s   1205.81
        NIST256p:     64   0.00053s   1892.05   0.00058s   1735.23   0.00106s    944.82
        NIST384p:     96   0.00110s    904.98   0.00118s    847.82   0.00217s    460.26
        NIST521p:    132   0.00234s    428.24   0.00245s    408.54   0.00443s    225.95
       SECP256k1:     64   0.00053s   1891.93   0.00058s   1734.46   0.00109s    913.35
 BRAINPOOLP160r1:     40   0.00025s   3982.49   0.00029s   3490.15   0.00053s   1878.51
 BRAINPOOLP192r1:     48   0.00032s   3086.07   0.00036s   2761.68   0.00063s   1578.22
 BRAINPOOLP224r1:     56   0.00041s   2412.41   0.00046s   2185.52   0.00076s   1311.65
 BRAINPOOLP256r1:     64   0.00054s   1866.84   0.00058s   1719.45   0.00110s    906.85
 BRAINPOOLP320r1:     80   0.00077s   1306.97   0.00083s   1201.59   0.00158s    632.82
 BRAINPOOLP384r1:     96   0.00112s    892.44   0.00119s    841.48   0.00229s    436.71
 BRAINPOOLP512r1:    128   0.00214s    467.05   0.00226s    441.64   0.00422s    237.13
```
To test performance with `gmpy2` loaded, use `tox -e speedgmpy2`.
On the same machine I'm getting the following performance with `gmpy2`:
```
                  siglen    keygen   keygen/s      sign     sign/s    verify   verify/s
        NIST192p:     48   0.00016s   6180.12   0.00017s   5846.30   0.00033s   3029.51
        NIST224p:     56   0.00021s   4861.86   0.00021s   4662.63   0.00042s   2366.47
        NIST256p:     64   0.00023s   4343.93   0.00024s   4152.79   0.00047s   2148.83
        NIST384p:     96   0.00040s   2507.97   0.00041s   2435.99   0.00079s   1260.01
        NIST521p:    132   0.00088s   1135.13   0.00089s   1121.94   0.00139s    721.07
       SECP256k1:     64   0.00023s   4360.83   0.00024s   4147.61   0.00044s   2254.53
 BRAINPOOLP160r1:     40   0.00014s   7261.37   0.00015s   6824.47   0.00031s   3248.21
 BRAINPOOLP192r1:     48   0.00016s   6219.18   0.00017s   5862.93   0.00034s   2933.74
 BRAINPOOLP224r1:     56   0.00021s   4876.48   0.00022s   4640.40   0.00041s   2413.48
 BRAINPOOLP256r1:     64   0.00023s   4397.89   0.00024s   4178.48   0.00044s   2272.76
 BRAINPOOLP320r1:     80   0.00031s   3246.64   0.00032s   3138.38   0.00063s   1593.14
 BRAINPOOLP384r1:     96   0.00040s   2491.04   0.00041s   2421.67   0.00079s   1262.64
 BRAINPOOLP512r1:    128   0.00062s   1618.30   0.00063s   1577.42   0.00125s    799.29
```

(there's also `gmpy` version, execute it using `tox -e speedgmpy`)

For comparison, a highly optimised implementation (including curve-specific
assembly for some curves), like the one in OpenSSL, provides following
performance numbers on the same machine.
Run `openssl speed ecdsa` to reproduce it:
```
                              sign    verify    sign/s verify/s
 192 bits ecdsa (nistp192)   0.0002s   0.0002s   4785.6   5380.7
 224 bits ecdsa (nistp224)   0.0000s   0.0001s  22475.6   9822.0
 256 bits ecdsa (nistp256)   0.0000s   0.0001s  45069.6  14166.6
 384 bits ecdsa (nistp384)   0.0008s   0.0006s   1265.6   1648.1
 521 bits ecdsa (nistp521)   0.0003s   0.0005s   3753.1   1819.5
 256 bits ecdsa (brainpoolP256r1)   0.0003s   0.0003s   2983.5   3333.2
 384 bits ecdsa (brainpoolP384r1)   0.0008s   0.0007s   1258.8   1528.1
 512 bits ecdsa (brainpoolP512r1)   0.0015s   0.0012s    675.1    860.1
```

Keys and signature can be serialized in different ways (see Usage, below).
For a NIST192p key, the three basic representations require strings of the
following lengths (in bytes):

    to_string:  signkey= 24, verifykey= 48, signature=48
    compressed: signkey=n/a, verifykey= 25, signature=n/a
    DER:        signkey=106, verifykey= 80, signature=55
    PEM:        signkey=278, verifykey=162, (no support for PEM signatures)

## History

In 2006, Peter Pearson announced his pure-python implementation of ECDSA in a
[message to sci.crypt][1], available from his [download site][2]. In 2010,
Brian Warner wrote a wrapper around this code, to make it a bit easier and
safer to use. Hubert Kario then included an implementation of elliptic curve
cryptography that uses Jacobian coordinates internally, improving performance
about 20-fold. You are looking at the README for this wrapper.

[1]: http://www.derkeiler.com/Newsgroups/sci.crypt/2006-01/msg00651.html
[2]: http://webpages.charter.net/curryfans/peter/downloads.html

## Testing

To run the full test suite, do this:

    tox -e coverage

On an Intel Core i7 4790K @ 4.0GHz, the tests take about 16 seconds to execute.
The test suite uses
[`hypothesis`](https://github.com/HypothesisWorks/hypothesis) so there is some
inherent variability in the test suite execution time.

One part of `test_pyecdsa.py` checks compatibility with OpenSSL, by
running the "openssl" CLI tool, make sure it's in your `PATH` if you want
to test compatibility with it.

## Security

This library was not designed with security in mind. If you are processing
data that needs to be protected we suggest you use a quality wrapper around
OpenSSL. [pyca/cryptography](https://cryptography.io) is one example of such
a wrapper. The primary use-case of this library is as a portable library for
interoperability testing and as a teaching tool.

**This library does not protect against side channel attacks.**

Do not allow attackers to measure how long it takes you to generate a keypair
or sign a message. Do not allow attackers to run code on the same physical
machine when keypair generation or signing is taking place (this includes
virtual machines). Do not allow attackers to measure how much power your
computer uses while generating the keypair or signing a message. Do not allow
attackers to measure RF interference coming from your computer while generating
a keypair or signing a message. Note: just loading the private key will cause
keypair generation. Other operations or attack vectors may also be
vulnerable to attacks. **For a sophisticated attacker observing just one
operation with a private key will be sufficient to completely
reconstruct the private key**.

Please also note that any Pure-python cryptographic library will be vulnerable
to the same side channel attacks. This is because Python does not provide
side-channel secure primitives (with the exception of
[`hmac.compare_digest()`][3]), making side-channel secure programming
impossible.

This library depends upon a strong source of random numbers. Do not use it on
a system where `os.urandom()` does not provide cryptographically secure
random numbers.

[3]: https://docs.python.org/3/library/hmac.html#hmac.compare_digest

## Usage

You start by creating a `SigningKey`. You can use this to sign data, by passing
in data as a byte string and getting back the signature (also a byte string).
You can also ask a `SigningKey` to give you the corresponding `VerifyingKey`.
The `VerifyingKey` can be used to verify a signature, by passing it both the
data string and the signature byte string: it either returns True or raises
`BadSignatureError`.

```python
from ecdsa import SigningKey
sk = SigningKey.generate() # uses NIST192p
vk = sk.verifying_key
signature = sk.sign(b"message")
assert vk.verify(signature, b"message")
```

Each `SigningKey`/`VerifyingKey` is associated with a specific curve, like
NIST192p (the default one). Longer curves are more secure, but take longer to
use, and result in longer keys and signatures.

```python
from ecdsa import SigningKey, NIST384p
sk = SigningKey.generate(curve=NIST384p)
vk = sk.verifying_key
signature = sk.sign(b"message")
assert vk.verify(signature, b"message")
```

The `SigningKey` can be serialized into several different formats: the shortest
is to call `s=sk.to_string()`, and then re-create it with
`SigningKey.from_string(s, curve)` . This short form does not record the
curve, so you must be sure to pass to `from_string()` the same curve you used
for the original key. The short form of a NIST192p-based signing key is just 24
bytes long. If a point encoding is invalid or it does not lie on the specified
curve, `from_string()` will raise `MalformedPointError`.

```python
from ecdsa import SigningKey, NIST384p
sk = SigningKey.generate(curve=NIST384p)
sk_string = sk.to_string()
sk2 = SigningKey.from_string(sk_string, curve=NIST384p)
print(sk_string.hex())
print(sk2.to_string().hex())
```

Note: while the methods are called `to_string()` the type they return is
actually `bytes`, the "string" part is leftover from Python 2.

`sk.to_pem()` and `sk.to_der()` will serialize the signing key into the same
formats that OpenSSL uses. The PEM file looks like the familiar ASCII-armored
`"-----BEGIN EC PRIVATE KEY-----"` base64-encoded format, and the DER format
is a shorter binary form of the same data.
`SigningKey.from_pem()/.from_der()` will undo this serialization. These
formats include the curve name, so you do not need to pass in a curve
identifier to the deserializer. In case the file is malformed `from_der()`
and `from_pem()` will raise `UnexpectedDER` or` MalformedPointError`.

```python
from ecdsa import SigningKey, NIST384p
sk = SigningKey.generate(curve=NIST384p)
sk_pem = sk.to_pem()
sk2 = SigningKey.from_pem(sk_pem)
# sk and sk2 are the same key
```

Likewise, the `VerifyingKey` can be serialized in the same way:
`vk.to_string()/VerifyingKey.from_string()`, `to_pem()/from_pem()`, and
`to_der()/from_der()`. The same `curve=` argument is needed for
`VerifyingKey.from_string()`.

```python
from ecdsa import SigningKey, VerifyingKey, NIST384p
sk = SigningKey.generate(curve=NIST384p)
vk = sk.verifying_key
vk_string = vk.to_string()
vk2 = VerifyingKey.from_string(vk_string, curve=NIST384p)
# vk and vk2 are the same key

from ecdsa import SigningKey, VerifyingKey, NIST384p
sk = SigningKey.generate(curve=NIST384p)
vk = sk.verifying_key
vk_pem = vk.to_pem()
vk2 = VerifyingKey.from_pem(vk_pem)
# vk and vk2 are the same key
```

There are a couple of different ways to compute a signature. Fundamentally,
ECDSA takes a number that represents the data being signed, and returns a
pair of numbers that represent the signature. The `hashfunc=` argument to
`sk.sign()` and `vk.verify()` is used to turn an arbitrary string into
fixed-length digest, which is then turned into a number that ECDSA can sign,
and both sign and verify must use the same approach. The default value is
`hashlib.sha1`, but if you use NIST256p or a longer curve, you can use
`hashlib.sha256` instead.

There are also multiple ways to represent a signature. The default
`sk.sign()` and `vk.verify()` methods present it as a short string, for
simplicity and minimal overhead. To use a different scheme, use the
`sk.sign(sigencode=)` and `vk.verify(sigdecode=)` arguments. There are helper
functions in the `ecdsa.util` module that can be useful here.

It is also possible to create a `SigningKey` from a "seed", which is
deterministic. This can be used in protocols where you want to derive
consistent signing keys from some other secret, for example when you want
three separate keys and only want to store a single master secret. You should
start with a uniformly-distributed unguessable seed with about `curve.baselen`
bytes of entropy, and then use one of the helper functions in `ecdsa.util` to
convert it into an integer in the correct range, and then finally pass it
into `SigningKey.from_secret_exponent()`, like this:

```python
import os
from ecdsa import NIST384p, SigningKey
from ecdsa.util import randrange_from_seed__trytryagain

def make_key(seed):
  secexp = randrange_from_seed__trytryagain(seed, NIST384p.order)
  return SigningKey.from_secret_exponent(secexp, curve=NIST384p)

seed = os.urandom(NIST384p.baselen) # or other starting point
sk1a = make_key(seed)
sk1b = make_key(seed)
# note: sk1a and sk1b are the same key
assert sk1a.to_string() == sk1b.to_string()
sk2 = make_key(b"2-"+seed)  # different key
assert sk1a.to_string() != sk2.to_string()
```

## OpenSSL Compatibility

To produce signatures that can be verified by OpenSSL tools, or to verify
signatures that were produced by those tools, use:

```python
# openssl ecparam -name prime256v1 -genkey -out sk.pem
# openssl ec -in sk.pem -pubout -out vk.pem
# echo "data for signing" > data
# openssl dgst -sha256 -sign sk.pem -out data.sig data
# openssl dgst -sha256 -verify vk.pem -signature data.sig data
# openssl dgst -sha256 -prverify sk.pem -signature data.sig data

import hashlib
from ecdsa import SigningKey, VerifyingKey
from ecdsa.util import sigencode_der, sigdecode_der

with open("vk.pem") as f:
   vk = VerifyingKey.from_pem(f.read())

with open("data", "rb") as f:
   data = f.read()

with open("data.sig", "rb") as f:
   signature = f.read()

assert vk.verify(signature, data, hashlib.sha256, sigdecode=sigdecode_der)

with open("sk.pem") as f:
   sk = SigningKey.from_pem(f.read(), hashlib.sha256)

new_signature = sk.sign_deterministic(data, sigencode=sigencode_der)

with open("data.sig2", "wb") as f:
   f.write(new_signature)

# openssl dgst -sha256 -verify vk.pem -signature data.sig2 data
```

Note: if compatibility with OpenSSL 1.0.0 or earlier is necessary, the
`sigencode_string` and `sigdecode_string` from `ecdsa.util` can be used for
respectively writing and reading the signatures.

The keys also can be written in format that openssl can handle:

```python
from ecdsa import SigningKey, VerifyingKey

with open("sk.pem") as f:
    sk = SigningKey.from_pem(f.read())
with open("sk.pem", "wb") as f:
    f.write(sk.to_pem())

with open("vk.pem") as f:
    vk = VerifyingKey.from_pem(f.read())
with open("vk.pem", "wb") as f:
    f.write(vk.to_pem())
```

## Entropy

Creating a signing key with `SigningKey.generate()` requires some form of
entropy (as opposed to
`from_secret_exponent`/`from_string`/`from_der`/`from_pem`,
which are deterministic and do not require an entropy source). The default
source is `os.urandom()`, but you can pass any other function that behaves
like `os.urandom` as the `entropy=` argument to do something different. This
may be useful in unit tests, where you want to achieve repeatable results. The
`ecdsa.util.PRNG` utility is handy here: it takes a seed and produces a strong
pseudo-random stream from it:

```python
from ecdsa.util import PRNG
from ecdsa import SigningKey
rng1 = PRNG(b"seed")
sk1 = SigningKey.generate(entropy=rng1)
rng2 = PRNG(b"seed")
sk2 = SigningKey.generate(entropy=rng2)
# sk1 and sk2 are the same key
```

Likewise, ECDSA signature generation requires a random number, and each
signature must use a different one (using the same number twice will
immediately reveal the private signing key). The `sk.sign()` method takes an
`entropy=` argument which behaves the same as `SigningKey.generate(entropy=)`.

## Deterministic Signatures

If you call `SigningKey.sign_deterministic(data)` instead of `.sign(data)`,
the code will generate a deterministic signature instead of a random one.
This uses the algorithm from RFC6979 to safely generate a unique `k` value,
derived from the private key and the message being signed. Each time you sign
the same message with the same key, you will get the same signature (using
the same `k`).

This may become the default in a future version, as it is not vulnerable to
failures of the entropy source.

## Examples

Create a NIST192p keypair and immediately save both to disk:

```python
from ecdsa import SigningKey
sk = SigningKey.generate()
vk = sk.verifying_key
with open("private.pem", "wb") as f:
    f.write(sk.to_pem())
with open("public.pem", "wb") as f:
    f.write(vk.to_pem())
```

Load a signing key from disk, use it to sign a message (using SHA-1), and write
the signature to disk:

```python
from ecdsa import SigningKey
with open("private.pem") as f:
    sk = SigningKey.from_pem(f.read())
with open("message", "rb") as f:
    message = f.read()
sig = sk.sign(message)
with open("signature", "wb") as f:
    f.write(sig)
```

Load the verifying key, message, and signature from disk, and verify the
signature (assume SHA-1 hash):

```python
from ecdsa import VerifyingKey, BadSignatureError
vk = VerifyingKey.from_pem(open("public.pem").read())
with open("message", "rb") as f:
    message = f.read()
with open("signature", "rb") as f:
    sig = f.read()
try:
    vk.verify(sig, message)
    print "good signature"
except BadSignatureError:
    print "BAD SIGNATURE"
```

Create a NIST521p keypair:

```python
from ecdsa import SigningKey, NIST521p
sk = SigningKey.generate(curve=NIST521p)
vk = sk.verifying_key
```

Create three independent signing keys from a master seed:

```python
from ecdsa import NIST192p, SigningKey
from ecdsa.util import randrange_from_seed__trytryagain

def make_key_from_seed(seed, curve=NIST192p):
    secexp = randrange_from_seed__trytryagain(seed, curve.order)
    return SigningKey.from_secret_exponent(secexp, curve)

sk1 = make_key_from_seed("1:%s" % seed)
sk2 = make_key_from_seed("2:%s" % seed)
sk3 = make_key_from_seed("3:%s" % seed)
```

Load a verifying key from disk and print it using hex encoding in
uncompressed and compressed format (defined in X9.62 and SEC1 standards):

```python
from ecdsa import VerifyingKey

with open("public.pem") as f:
    vk = VerifyingKey.from_pem(f.read())

print("uncompressed: {0}".format(vk.to_string("uncompressed").hex()))
print("compressed: {0}".format(vk.to_string("compressed").hex()))
```

Load a verifying key from a hex string from compressed format, output
uncompressed:

```python
from ecdsa import VerifyingKey, NIST256p

comp_str = '022799c0d0ee09772fdd337d4f28dc155581951d07082fb19a38aa396b67e77759'
vk = VerifyingKey.from_string(bytearray.fromhex(comp_str), curve=NIST256p)
print(vk.to_string("uncompressed").hex())
```

ECDH key exchange with remote party

```python
from ecdsa import ECDH, NIST256p

ecdh = ECDH(curve=NIST256p)
ecdh.generate_private_key()
local_public_key = ecdh.get_public_key()
#send `local_public_key` to remote party and receive `remote_public_key` from remote party
with open("remote_public_key.pem") as e:
    remote_public_key = e.read()
ecdh.load_received_public_key_pem(remote_public_key)
secret = ecdh.generate_sharedsecret_bytes()
```
