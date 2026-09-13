[Repo](https://github.com/filosottile/age)

**age** is a small, modern command-line tool for **encrypting files**.

> **Encrypt a file so that only someone with the corresponding private key can decrypt it.**

```

`age` uses a **public/private key pair**:

- **Public key** → used to encrypt; safe to share.
    
- **Private key** → used to decrypt; must be kept secret.
```


The main reason to use **age instead of something like GPG** is that age was designed to be **simple, modern, and easy to use**, particularly for encrypting files and secrets.

You can create a new pair of keys by running the following command:

```sh
age-keygen -o <path to your keys>
```

You can read your existing keys by running the following command:

```sh
age-keygen -y <path to your keys>
```
