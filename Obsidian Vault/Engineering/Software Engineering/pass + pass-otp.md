## pass
`pass` is essentially a command-line interface around **GnuPG-encrypted password files**. A typical password entry might look conceptually like:

```text
my-google-account@example.com
correct-horse-battery-staple
```

The first line is conventionally the password, while additional lines can contain notes or other secrets. For example:

```bash
pass insert google/account
```

and then enter the Google password. You can retrieve it with:

```bash
pass google/account
```

or copy just the password to the clipboard with:

```bash
pass -c google/account
```

## `pass-otp`

Google Authenticator-style MFA commonly uses **TOTP**: **T**ime-**O**ne-**T**ime **P**assword. When you enable this on an account, the service gives you a secret key, often represented as something like:

```text
JBSWY3DPEHPK3PXP
```

Your authenticator and Google's servers independently use that secret plus the current time to calculate the same short-lived code:

```text
482913
```

The code changes periodically, typically every 30 seconds. `pass-otp` lets you associate that TOTP secret with a `pass` entry and generate the current code from the command line. Conceptually:

```bash
pass otp google/account
```

might output:

```text
482913
```

The important distinction is:

> **The password is one secret; the TOTP seed is a second secret.**  
> `pass` can securely store both, while `pass-otp` knows how to turn the TOTP seed into the current six-digit code.

## Setting up Google MFA with `pass` + `pass-otp`

Suppose you have a Google account and want your computer to act as your authenticator.

### Step 1: Enable 2-Step Verification in Google

In your Google Account security settings, enable **2-Step Verification** and choose the option to configure an **Authenticator**. Google will show you a QR code containing the TOTP configuration. Normally you'd scan that QR code with Google Authenticator, 1Password, Bitwarden, etc. Instead, if Google gives you the underlying setup key, you can put that secret into `pass-otp`. The setup key is the valuable part—not the QR image itself.

### Step 2: Put the TOTP secret into `pass`

For example, suppose Google gives you:

```text
JBSWY3DPEHPK3PXP
```

You would associate it with your existing password entry using `pass-otp`'s TOTP commands.

## Reading an MFA QR Code for `pass-otp`

When setting up TOTP-based MFA, services such as Google can provide an authenticator configuration as a QR code. The QR code normally contains an `otpauth://` URI. We can use Python and `pyzbar` to decode the QR code, then use the resulting URI to configure `pass-otp`.

Install Python dependencies:

```bash
pip install pillow pyzbar
```

`pyzbar` is a Python wrapper around the ZBar barcode/QR-code library. Save the MFA QR code as an image. Then use:

```python
from PIL import Image
from pyzbar.pyzbar import decode

image = Image.open("google-mfa.png")

codes = decode(image)

if not codes:
    raise RuntimeError("No QR code found")

uri = codes[0].data.decode("utf-8")

print(uri)
```

For a TOTP authenticator QR code, the result should look approximately like:

```text
otpauth://totp/Google%3Aalice%40example.com?secret=JBSWY3DPEHPK3PXP&issuer=Google
```

The important part is that this is an **`otpauth://` URI**, not merely a six-digit OTP code. The `secret` contained in the URI is the TOTP seed that `pass-otp` needs.  Then use its `insert` functionality to add the TOTP configuration to the appropriate `pass` entry.
