class: center, middle
name: title

# Your org's Tech Security Hygiene, Part 2

???

Last week we covered equipment and usage security, today we will cover
how we manage credentials and work more securely while working remotely at your org's.

---
class: middle
name: agenda

## Agenda

Definition:
  * Credential

Part 2 (another day):
* Credential management
* Working remotely

???

When we create, use, share, and update credentials for your org's we need to be
cognizant of security for the protection of the entire organization.

* Sharing credentials within your org's
* Signing statements (verify identity)
* SSH/PGP keys
* Dos and don'ts.

When we work remotely, there are some things we should consider
in terms of our behavior to limit exposure to information leakage
(e.g. your org's data, your org's secrets, your org's credentials) or being susceptible to installing
malware, spyware, ransomware with poor public network configuration and/or
behavior.

Sometimes it's important to know who created content with reasonably high
assurances. e.g. canary warrants for users and/or employees, audit for
backdoor commits.

---
class: middle
name: credential

## Definition: Credential

Typically a combination of an identity and first factor authenatication means.

```haskell
import Skillshare.LastWeek (Identity(..), Authenatication(..))
```

For those that think in strongly-typed forms:

```haskell
data Credential =
  Credential { identity :: Identity, authentication :: Authenatication }
```

Or for the weakly-typed, we can just define this as a type alias of a tuple:

```haskell
type Credential = (Identity, Authentication)
```

---

## Sharing credentials within your org's: Overview

* Currently we use LastPass for shared web-based passwords.
* Credentials used by our systems (infrastructure and applications) are
  sometimes sprawled across repository in the clear. We need to fix this
  going forward.
* Some repositories (e.g. usermetrics, logging) use PGP encrypted files
  and scripts to put secrets in ASCII files (`--armor`) with a key ring
  that has all relevant parties' public keys so that any of them can decrypt
  when needed.
* Still have need for one-off password sharing, e.g. resetting AWS console
  account passwords (it doesn't have an email reset, really??? _*sigh*_)
* Keybase is also a good way for us to get started with PGP encryption and
  signing. Can offer a solution to secure temporary credential sharing
  for password reset cases, etc.

---
class: middle
name: lastpass

## LastPass

* Everyone should be setup with 2FA with a private user account with in the
  LastPass Enterprise plan so we can share credentials with each other better.
* Everyone should have already setup 2FA or is waiting on a Yubikey to set
  2FA up at this point.
* Ideal for sharing web-based credentials in shared folders for each team's
  purposes.
* Not ideal for managing credentials used inside our software/systems, e.g.
  API tokens for our external service dependencies (e.g. DataDog, New Relic,
  Airbrake, Sendgrid, Akismet, ActionNetwork, etc.).

???

---
class: middle
name: programmatic-credentials

## Programmatic Credentials

* We have credentials littered over our repositories, typically in the clear.
* Since we use a hosted code management solution this is a concern for multiple
  reasons:
  * Do we trust every employee at GitHub?
  * GitHub has had it's _authorization_ systems (written in Rails) compromised
    before (c2012, due to GH not using `attr_accessible`) where hacker gained
    write access to the `rails/rails` repository to demonstrate the exploit:
    https://gist.github.com/peternixey/1978249
  * Shit happens! Do we even trust ourselves to manage our private repositories
    as tightly as we would like? (It's not personal. `:tinfoil:`)
* There are many ways we can keep our API tokens not in plain text inside of
  external vendor services like GitHub (or until recently Heroku).


---
class: middle
name: programmatic-credential-encryption-choices-1

## Programmatic Credential Encryption Choices

In source/reopsitory choices:

* KeePass, Gorilla, passwd, etc: password managers via files with shared or asymmetric
  key methods of encryption, which either have command-line interfaces or APIs
  that can be automated around.
* Chef encrypted data bags: used already at your org's. Sometimes it makes sense to
  keep secrets in infrastructure code, sometimes it doesn't fit well. Think
  about how you deliver your software and your CI/CD pipeline.
* `git-secret`: http://git-secret.io/#usage Haven't used yet but looks promising.
* `git-crypt`: has pros/cons, not as simple as it sounds in the README.
* Using gitignore, possibly git-attributes, gpg, together. Requires some
  scripting. Look at usermetrics, dailykos_logging for some script examples.
  Uses multiple forms of signing and automates verification of signatures
  on encrypted documents better than most git-* packaged solutions IMO.

When using shared key based encryption, we need to think about how to transfer
the shared key. LastPass is a possibility via notes, for example.

---
class: middle
name: programmatic-credential-encryption-choices-2

## Programmatic Credential Encryption Choices

Infrastructure-based choices:

* AWS KMS: AWS-managed, assume AWS knows what they are doing but extra
  vendor-specific dependency; has a cost.
* Hashicorp Vault: self-hosted on our infrastructure, extra overhead. Getting
  configuration of Vault right in terms of how we setup our cloud resources
  to support it might be onerous.

Infractructure-based  solutions might be more beneficial in cases where
you are negotiating and generating keypairs with another party programmatically,
e.g. ACME TLS certificates on provisioning of new images/instances, etc.

---

## Credential Management: Dos and Don'ts

*  *Do* setup 2FA for LastPass and other critical your org's accounts (this is already
  under way at your org's)
* Generally, *don't* share accounts when possible. Vendors need to support
  notion of organization account with user accounts tied to it. Examples
  of such accounts are Google Apps, DataDog, etc.
* *Don't* share credentials in the clear over Slack. It might be encrypted
  in transit, but it is decryptable by Slack's systems.
* *Do* use LastPass for sharing (with humans @ your org's) credentials, individually
  or with the group when appropriate.
* *Do* use end-to-end secure means of transferring credentials for one-off
  purposes (e.g. temporary reset passwords). e.g. LastPass sharing with
  one user privately, Signal (if both parties use this for messaging already)
  with a short message TTL timeout, encrypt via Keybase and send encrypted
  *and* signed message via Slack.
* *Do* generate a random, unique passwords that are 16 characters or more for
  private accounts via LastPass for your private your org's accounts. *Don't* share
  these with anyone.
* *Don't* ever share your private SSH or PGP keys with anyone else. Take care
  to make sure you are only sharing public keys with others. Even coworkers.
  :)
* *Do* factor out secrets outside of the code into configuration where
  encrypted files are referenced.

---

## Working Remotely: Dos and Don'ts

* *Don't* use public or shared networks whenever possible.
* *Do* use HTTPS as much as possible.
* *Do* use Internet connectivity VPN when useful (e.g. geo debugging).
* *Don't* leak information via your host/computer name on the local network
  on shared or public networks or make it obvious you where you work.
* *Do* consider using DNScrypt proxy or a logless DNS resolver on a VPN when
  on shared or public networks.
* *Don't* trust ISP DNS resolvers. Keep a list of open public DNS resolver
  IPs you consider trust-worthy (e.g. Google?, OpenDNS, etc.). Applies on
  your home network as much as public or shared networks you don't control.
* *Do* assess 3rd-party risk periodically for each party used (DNS resolvers,
  VPN services for geo purposes, public WiFi networks).

---

## Signing Work: Dos and Don'ts

* *Do* sign secrets when you encrypt them against a key ring. It allows those
  who decrypt to make sure a trusted party encrypted the secrets.
* *Do* sign your Git commits. You can sign your git commits which is an
  additional assurance that someone didn't add a commit on laptop that has
  a git clone and authorization to push to the repository.
* *Do* require a passphrase for your PGP/GPG and/or SSH keys with small enough
  timeouts on your GPG/SSH agents to be reasonably security and also
  usable/productive.

