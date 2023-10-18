#Tutorial for create a VSCode environment for signed commit

Git provides the possibility to verify that commits are actually from a trusted source using the GNU Privacy Guard (GPG). GitHub marks signed commits with a green “verified” badge.

To enable GPG verified commit we need to setup GPG, first verify gpg is currently installed:

## Check GPG version:

### ([GnuPG - Download](https://www.gnupg.org/download/))

```bash
$ gpg --version
gpg (GnuPG) 2.2.19
libgcrypt 1.8.5
```

## Check stored keys (public.key and private.key):

If keys are not available create new one ([Generating a new GPG key - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)):

#### Generating a GPG key:

```bash
$ gpg --full-generate-key 
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
  (14) Existing key from card
Your selection? 
```

Press Enter to accept the default:

```bash
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (3072)
```

Press Enter to accept the default:

```bash
Requested keysize is 3072 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 
```

Press Enter to accept the default:

```bash
Key does not expire at all
Is this correct? (y/N) 
```

Press Y to confirm key will never expire

Enter your user ID information.

**Note:** When asked to enter your email address, ensure that you enter the verified email address for your GitHub account. To keep your email address private, use your GitHub-provided `no-reply` email address. For more information, see "[Verifying your email address](https://docs.github.com/en/get-started/signing-up-for-github/verifying-your-email-address)" and "[Setting your commit email address](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-personal-account-on-github/managing-email-preferences/setting-your-commit-email-address)."

To get the no-reply email address, go to  [github mail settings](https://github.com/settings/emails) and set "Keep my email address private"

```bash
GnuPG needs to construct a user ID to identify your key.

Real name: Name Surname
Email address: email@domain.com
Comment: 
You selected this USER-ID:
    "Name Surname <email@domain.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
```

Perss O to confirm

A new pair set keys is now created:

```bash
gpg: key 1455AF6740622E54 marked as ultimately trusted
gpg: directory '/home/alessandro/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/home/user/.gnupg/openpgp-revocs.d/AB3354129A1DB341CACCDDE4A321BB2091582E22.rev'
public and secret key created and signed.

pub   rsa3072 2023-10-18 [SC]
      AB3354129A1DB341CACCDDE4A321BB2091582E22
uid                      Name Surname <email@domain.com>
sub   rsa3072 2023-10-18 [E]
```

Type a secure passphrase when prompted

Use the 

```bash
$ gpg --list-secret-keys --keyid-format=long
```

command to list the long form of the GPG keys for which you have both a public and private key. A private key is required for signing commits or tags.

```bash
gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: pgp
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
/home/alessandro/.gnupg/pubring.kbx
-----------------------------------
sec   rsa3072/123ABA404A236F21 2023-10-18 [SC]
      AB3354129A1DB341CACCDDE4A321BB2091582E22
uid                 [ultimate] Name Surname <email@domain.com>
ssb   rsa3072/1129A334ABB1F378 2023-10-18 [E]
```

Paste the text below, substituting in the GPG key ID you'd like to use. In this example, the GPG key ID is `123ABA404A236F21`:

```bash
$ gpg --armor --export 123ABA404A236F21


-----BEGIN PGP PUBLIC KEY BLOCK-----

--- CUT ---

-----END PGP PUBLIC KEY BLOCK-----
```

Copy your GPG key, beginning with `-----BEGIN PGP PUBLIC KEY BLOCK-----` and ending with `-----END PGP PUBLIC KEY BLOCK-----`.

## [Adding a GPG key to your GitHub account - GitHub Docs](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account)):

Go to [github keys settings](https://github.com/settings/keys), next to the "GPG keys" header, click **New GPG key**.

In the "Title" field, type a name for your GPG key.

In the "Key" field, paste the GPG key you copied when you [generated your GPG key](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key).

Click **Add GPG key**.

To confirm the action, authenticate to your GitHub account.

## Configure Git to use signing key ID:

Signing key ID is a 16-digit alphanumeric string that can be found with:

```bash
$ gpg --list-signatures
/home/user/.gnupg/pubring.kbx
-----------------------------------
pub   rsa3072 2023-10-18 [SC]
      AB3354129A1DB341CACCDDE4A321BB2091582E22
uid           [ultimate] Name Surname <email@domain.com>
sig 3        123ABA404A236F21 2023-10-18  Name Surname <email@domain.com>
sub   rsa3072 2023-10-18 [E]
sig          123ABA404A236F21 2023-10-18  Name Surname <email@domain.com>
```

Set Git:

```bash
git config --global user.signingkey 123ABA404A236F21
```

you can tell Git to sign commits per default (since Git 2.0), so you don’t always have to add the `-s` flag in the command line:

```bash
git config --global commit.gpgsign true
```

## Configure VSCode:

You now need to tell to VSCode to submit signed commit, append `-s` flag to `git commit` command.

Open gpg settings and check 'Enables commit signing with GPG', or add this line in settings.json

```
"git.enableCommitSigning": true
```