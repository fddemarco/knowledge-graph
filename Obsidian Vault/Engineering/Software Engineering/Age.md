[Repo](https://github.com/filosottile/age)

**age** is a small, modern command-line tool for **encrypting files**.

> **Encrypt a file so that only someone with the corresponding private key can decrypt it.**

```

`age` uses a **public/private key pair**:

- **Public key** → used to encrypt; safe to share.
    
- **Private key** → used to decrypt; must be kept secret.
```

you can put that in `.sops.yaml` and commit it. Your private key stays on your machine (or securely backed up elsewhere).

The main reason to use **age instead of something like GPG** is that age was designed to be **simple, modern, and easy to use**, particularly for encrypting files and secrets.
