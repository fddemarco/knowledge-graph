[SOPs Installation](https://github.com/getsops/sops/releases)

**SOPS** is an editor of encrypted files that supports YAML, JSON, ENV, INI and BINARY formats and encrypts with AWS KMS, GCP KMS, Azure Key Vault, HuaweiCloud KMS, age, and PGP.

> **SOPS lets you keep secrets in a Git repository, but encrypted.**

For example, you might have:

```
DB_HOST=localhost
DB_USER=myapp
DB_PASSWORD=super-secret
API_KEY=abc123
```

You don't want that plaintext `.env` in Git.

With SOPS, you can create:

```
.env.enc
```

containing encrypted values:

```
DB_HOST: ENC[AES256_GCM,...]
DB_USER: ENC[AES256_GCM,...]
DB_PASSWORD: ENC[AES256_GCM,...]
API_KEY: ENC[AES256_GCM,...]
```

That encrypted file **can be committed to Git**.

### SOPS + `age`

SOPS isn't itself the underlying encryption algorithm. It uses encryption systems such as **age** to protect the data.

```
                SOPS
                 │
        manages the secret file
                 │
                 ▼
                age
                 │
          encrypts/decrypts
                 │
                 ▼
             .env.enc
```

You can then do:

```
sops .env.enc
```

to edit the encrypted file, or:

```
sops decrypt .env.enc > .env
```

to recover the plaintext `.env`.

### Why SOPS instead of just encrypting the file?

SOPS is designed specifically for **configuration files and secrets**.

It understands formats like:

```
YAML
JSON
ENV
INI
dotenv
```

and can encrypt the **values while preserving the structure**.

That's useful because your Git repository can still contain something conceptually like:

```
database:
  host: mydb.example.com
  username: myapp
  password: ENC[...]
```

rather than an opaque encrypted blob.
