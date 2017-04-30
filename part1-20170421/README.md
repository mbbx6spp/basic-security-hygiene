class: center, middle
name: title

# Your org's Tech Security Hygiene

???

Security of a team or organization is only as good as it's
weakest link. Therefore, every single one of us needs to
huddle together to improve our own online and offline
computer security hygiene setup and habits going forward.

---
class: middle
name: nothing-is-secure

# Absolutely nothing is secure

It's all relative and within certain bounds. Everything can be:

* hacked
* cracked
* subverted

---
class: middle

# But we can make it expensive as hell

Not even state-sponsored actors have infinite resources, so we can make it as
expensive as feasibly possible for malicious threat actors to gain access to
Your org's's secrets and data.

---
name: agenda

## Agenda

Part 1 (today):
* Scope
* Concepts
* Bottom up (on OSX)
* Privacy basics

Part 2 (another day):
* Credential management
* Working remotely
* Signing work (might put this into PGP/GPG talk?)

???

## Detailed Outline

* Scope
* Concepts
  * Identify threats
  * Understand difference between identity, authentication, authorization
* Starting bottom up (general online hygiene)
  * Understand your equipment (hardware/firmware level)
  * Understand your operating system
  * Understand your network (firewall settings, DNS, IPv4 vs IPv6 issues, etc.)
  * Web browsing hygiene (HTTPS, understand certs, ...)
  * Identity system level threats (OpenSSL is a plague of insecurity built into everything:( )
  * Stay updated (firmware, OS, browsers)
* Privacy basics
  * Suggested tools
  * Things to beware
* Credential/key management
  * Sharing credentials within Your org's
  * Signing statements (verify identity)
  * SSH/PGP keys
  * Dos and don'ts.
* Working remotely (and/or traveling)
  * Threats of public or unknown networks
* Sign your work (optional)

---
class: center, middle
name: scope

## Scope

???

There are many facets of security that different people at Your org's will be
involved with in different ways, such as:

* application development security
* data handling
* server infrastructure security (authN, sandboxing, etc.)
* creating and maintaining ACLs (authZ) for various services (internal and external)
* on-boarding / off-boarding
* evaluating security of vendors and external partners

The above will not be covered in this skillshare.

*This skillshare is related to increasing tech staff awareness of their
own developer setup, equipment, behaviors and ways in which each one of
us can raise the bar on our team or organizational security together.

---
class: center, middle
name: concepts

## Concept

???

Here we will introduce the different concepts to building a more holistic
mental model to being more aware of your online and offline exposure to
different threats.

Our goal is to minimize our exposure to identified _threats_ without
halting our tech staff's productivity.

As a result most of the methods to eliminating or reducing surface area
of exposure to threats are recommended at this point.

---
class: center, middle

### Threat

A _threat_ is a possible danger that might exploit a vulnerability to breach
security and therefore cause possible harm.

???

When we identify threats we can then evaluate what is realistic to guard
against and what is unrealistic. Our goal at Your org's with this security initiative
is to increase security _hygiene_ (for lack of a better word) awareness,
educate staff (based on their technical skill levels) practical security
methods and habits to eliminate or reduce exposure to identified threats.

With this in mind we need to *be realistic*.

Ideally we want to prevent all unauthorized access to Your org's systems or
information leaks. Realistically, if Your org's is targeted by three letter acronym
state sponsored agencies it would be counter productive to take _all_
necessary measures to do this. i.e. it would be next to impossible to
get work done at Your org's.

---

## Identified Preventable Threats

* virus/malware/ransomware controllers
* commercial scammers
* unwitting opportunistic thieves
* competitor espionage
* shared/public network information leakers (third party, often clumsy, uninentional)
* non state-sponsored political adversaries
* hackers targeting Your org's (realistic goal: increase costs to be undesirable by actors)

???

As a result we will focus on eliminating threat actors like the above
whenever possible.

We also want to increase the cost of targeted surviellance by three
letter state sponsored agencies without impacting productivity too much. :)

* We care about preventing:
  * unauthorized access of our systems and/or data
  * unauthorized *use* of our systems and/or data
  * information leaks

Technical staff at Your org's (even if not involved in operations or infrastructure
systems directly) can massively reduce the exposed surface area of potential
threats by following some common sense rules.

This will hopefully act as a set of guidelines for that.

---
class: center, middle

### Identity, Authentication, Authorization + 2FA

???

Reference:
https://technet.microsoft.com/en-us/library/cc512578.aspx

---

class: middle

#### Identity: _"Who are you?"_

* provided by the _"user"_ (formally user is called a principal and refers to
both human and non-human actors in a system):
* public identifier
* examples:
  * login on a UNIX system
  * nickname on Your org's
  * screenname on Twitter
  * username on OSX
  * access key id for programmatic AWS (`AWS_ACCESS_KEY_ID`)
* don't need to keep this a secret

---

class: middle

#### Authentication: _"How do you prove your identity?"_

* provided by the _principal_ again
* secret response to satisfy proof requirement by the system
* examples:
  * password to access your bank account online
  * private key to allow you to SSH to systems
  * secret access token for programmatic AWS (`AWS_SECRET_ACCESS_TOKEN`)
  * NEVER biometric information (this is rife with problems)
* MUST be kept secret
* can add multiple factors (see below) as additional forms of authentication

---

class: middle

#### Authorization: _"What are you allowed to do?"_

* provided by the system
* token, ticket, or session granting for a period
* grant specific access to parts of the system
* examples:
  * Your org's tech team has staff access to the Your org's product, access to some admin features
  * our friends can sign up for a regular user account, diary and comment creation system access
  * anonymous users to Your org's have read access to public parts of the site

---

class: middle

#### Multiple Factors: _"How do you make sure only you can authenticate with a service?"_ (within reasonable bounds)

* additional form of authentication to ensure it really is the principal identified by identity
* interactive/challenge based
* expires relatively quickly
* examples:
  * system sends a text message to your mobile number after first factor authentication (this is not recommended for high risk accounts)
  * system can compute verification of a regularly changing code based on a scheme that only you should have physical access to like a hardware security key or authenticator-style apps on your phone.

---

class: middle

## Bottom up

Known your system bottom to top!

```haskell
exposure :: Exposure Hardware -- ^ I categorize this under 'Equipment' below
         -> Exposure Firmware -- ^ I categorize this under 'Equipment' below
         -> Exposure OperatingSystem -- ^ Whatever OS you use, OSX, Linux, OpenBSD; don't trust defaults!
         -> Exposure (Set Services) -- ^ The set of system and user services (agents of daemons)
         -> Exposure (Set Application) -- ^ The set of applications you install and run on your system
         -> Exposure YourUsage -- ^ How you use all of these things together
         -> Exposure (Set ExternalSystem) -- ^ How you interact with external systems -- OUT OF SCOPE
         -> Exposure WorkProduct
```

???

I had to creep in some Haskell/FP into this talk somehow. :)

What I want to point out from the type is that the configuration of all the
levels on up matter, as does how you use your system and related systems.

---

class: middle

### Equipment (hardware & firmware)

Suggestions, recommendations.

???

Bundling hardware and firmware together. Here are some ideas on how
you can reduce exposure to Your org's's identified threats by configuring
your equipment better.

Right now we leave it up to the developer to use judgement as to
what makes sense for your needs.

---

class: middle

#### Set firmware password

* What does it prevent?
  * Booting up devices other than the startup disk.
  * Can be setup to prompt on every boot up which can prevent anyone from
    just booting up to your user login prompt.
  * If your laptop is stolen is prevents the laptop being easily usable
    by the thief. More likely (even if remote) to result in recovery of laptop.

---

class: middle

#### Set firmware password

* How to do this? (Mac Book Pro)
  1. Boot while keeping `Cmd + R` pressed down.
  2. When Recovery window appears, select `Firmware Password Utility` from Utilities menu
  3. In firmware utility, select `Turn on Firmware Password`.
  4. Enter a new password, then enter same password. DON'T forget it.
  5. Select `Set Password`.
  6. Select `Quit Firmware Utility`.
  7. Select Apple menu and choose Restart or Shutdown.

---

class: middle

#### Set firmware password

* Alternative method via command-line (again Mac Book Pro):
  1. Store safely (e.g. your org's LastPass account) the recovery key
     in case you lock yourself out of your laptop (Apple Store visit).
```bash
sudo firmwarepasswd -unlockseed
```
  2. Generate a password for use on startup when changing disk boot order (only):
```bash
sudo firmwarepasswd -setpasswd -setmode command
# Enter the randomly generated unique passphrase twice when prompted
```
  3. Save the generated password in your org's LastPass account in a note.
  4. Verify you have set it correctly:
```bash
sudo firmwarepasswd -verify
# enter expected passphrase, should show "success" if verified.
```
  5. Check mode is what you just set it to:
```bash
sudo firmwarepasswd -mode
```

On next boot you wish to change the boot order, you will be prompted with this
passphrase.

---

class: middle

#### Set firmware password

Risks:
  * You forget your password, you will need to take it into Apple. If you don't
    get the unlock seed, I don't know if even they can help?

Recommendation:
  * You set the password for only startup disk order change (aka `command`).
  * Generate a 16-20 character unique random password in your LastPass Your org's
    Enterprise account and store it there. This will make sure you don't
    _just_ "forget" it and have a way (which is only mildly inconvenient)
    via another online device to access it.
  * Having the firmware password prompt EVERY boot without changing startup
    disk boot order is very annoying if you have full disk encryption (FDE)
    turned on (see later) because you will have to enter two passwords. One
    for the boot/firmware prompt and the other for the FDE passphrase used
    to decrypt your hard drive.

---

class: middle

#### Full Disk Encryption (FDE) -- Why

Turn this on. *Highly recommended.*

Why use FDE?
* Protects data at rest.
* A malicious user with physical access to your hardware could boot into a
  Live USB and mount your hard drive to view the files, assuming you didn't
  turn on FDE and/or not set firmware password on startup disk change.
  Having both might sound like overkill but they protect against slightly
  different threats (although there is an overlap too, like in this case).
* Increase costs of getting your data at rest.
* Relatively cheap method to protect Your org's data.

---
class: middle


#### Full Disk Encryption (FDE) -- How

How to turn this on for OSX?
* Seed your PRNG with "random" numbers. My recommendation:
  ```bash
  cat > /dev/random
  ```
  Then let your hamster, cat, or pet mouse run around on your keyboard!
  Or type _human randomness_ in for about 2 minutes.
* Start FDE setup with:
  ```bash
  sudo fdesetup enable
  ```
* Save your recovery key into your org's LastPass account as a note.
* Generate your passphrase in your org's LastPass account and store appropriately.

---

#### Full Disk Encryption (FDE) -- Caveats

Other considerations/recommendations:
  * Consider evicting the file vault keys from memory on hibernation and
    setting hiberation as default instead of sleep.
```bash
sudo pmset -a destroyfvkeyonstandby 1
sudo pmset -a hibernatemode 25
```

*DO NOT RECOMMEND: evicting FileVault keys in standby mode due to a problematic
Darwin bug.*

---
class: center,middle

### Operating System (OSX-centric)

---
class: middle

#### Refresh/reinstall Your System

Recommend doing so annually, or even quarterly. Your work laptop should
be cattle not a pet.

Why bother?

* So many things accrue on your system in so many levels of caches that can
  make your system more and more vulnerable to all kinds of threat exposures

---
class: middle

#### Refresh/reinstall Your System

How to do this?

* Enter Recovery Mode (Cmd + R on boot)
* Follow OSX Recovery prompts to reinstall your OS
* On first boot clear non-volatile random-access memory (NVRAM) by (`Cmd + Option + P + R`)
* If possible (non-touchbar MBPs), disconnect your laptop from any networks.

---
class: middle

#### Refresh/reinstall Your System

Avoid information leakage on shared networks:
* Update your computer name:

```bash
sudo scutil --set ComputerName my_computer_name
```

* Update your host name:

```bash
sudo scutil --set LocalHostName my_hostname
```

Make your new computer and host names only meaningful to you. They should
not contain your username or (obviously) any part of any of your passwords.
It should probably also not include anything highly identifying as organization
specific. A prefix, suffix, etc of `tla` is just fine though.

Before you connect to the network for the first time post-first boot, you
should also tighten your Firewall (see below).

---
class: center,middle
name: network

### Network

---
class: middle

#### Firewall

Always enable your firewall:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on
```

Recommend you enable logging for your firewall:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setloggingmode on
```

Disable ICMP ping responses (prevents some port scanning software detecting
your laptop exists):

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on
```

---
class: middle

#### Firewall

Other considerations:

* RECOMMENDED: Disable automatic safelisting of code signed downloaded software:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setallowsignedapp off
```

* Disable automatic safelisting of builtin software:

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setallowsigned off
```

Reload the firewall:

```bash
sudo pkill -HUP socketfilterfw
```

---
class: middle

#### Services & Ports

Be *HIGHLY* suspicious of your system daemons:

```bash
sudo launchctl list
```

Review your user-level agents:

```bash
launchctl list
```

---
class: middle

#### Services & Ports

How do you get acquainted with these services and understand whether they are
useful for your needs or just higher exposure to trouble? Here's my rough
heuristics (and how I learned the hard way when I used OSX >2+ years ago):

* Inspect the specific service info via:

```bash
launchctl list com.apple.Maps.mapspushd
```

* Check which user the process is running as:

```bash
pgrep -la -f mapspushd
```

* Search for the service name in your favorite search engine to find information
  about the process.

---
class: middle

#### Services & Ports

* Look at the property list (plist) defaults for the service:

```bash
# For system daemons
defaults read /System/Library/LaunchDaemons/bla.foo.bar.baz.plist

# For user agents provided by the system
defaults read /System/Library/LaunchAgents/bla.foo.bar.baz.plist

# For user agents provided by the current user
defaults read ~/Library/LaunchAgents/bla.foo.bar.baz.plist

# system daemons provided by the administrator
defaults read /Library/LaunchDaemons/bla.foo.bar.baz.plist
```

* Look at all the LISTEN ports on your host:

```bash
sudo lsof -iTCP -sTCP:LISTEN -P -n
```

* Try to match running services (agents/daemons) to the ports by running:

```bash
lsof -i tcp:PORTHERE
# or
lsof -i udp:PORTHERE
```

---
class: middle

#### Spoof MAC address

Why would you want to do this?
When connected to public/open/shared networks of dubious quality (e.g.
airport, coffeeshop, restaurant, megabus, amtrak), it can eliminate
geo trackers from determining your laptop's movements. More interestingly
(non-security related), it might allow you to connect for another 15 minutes
free WiFi :wink: depending on the system that tracks free time.

---
class: middle

#### Spoof MAC address

To generate a random MAC address you can do the following:

```bash
openssl rand -hex 6 | sed 's/\(..\)/\1:/g; s/.$//'
```

To update the MAC address for an interface (e.g. your wireless) you will want to do something like:

```bash
sudo ifconfig en1 ether NEWMAC
```

---
class: middle

#### Services & Ports

* Disable unneeded services (but search internet first to find out if this
  will cause operability problems with running applications first):

```bash
# Disable Bonjour service and helper service
# -w option will set the disabled flag to true for future disabling
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponder.plist
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.mDNSResponderHelper.plist
```

---
class: middle

#### IPv4 vs IPv6

Understanding IPv4:

* e.g. `127.0.0.1`, `10.43.11.55`, `8.8.4.4`
* familiar addressing scheme for many
* running out of space
* commonly multiple devices map to one public IP (NAT device)
* "cloaked" (lay person's view of setup)
* trackers can associate all traffic behind a NAT

---
class: middle

#### IPv4 vs IPv6

Understanding IPv6:

* e.g. `::`, `2a03:2880:f10b:83:face:b00c:0:25de`
* unfamiliar addressing scheme (and "uglier")
* lots of available IPv6 space
* one device will have many IPv6 addresses (one or more contiguous ranges?)
* today minimal geo location information for IPv6 ranges, but this is temporary
* partial/incomplete attempts to generate _privacy_ IPv6 IPs; creates false sense of privacy.

---
class: middle

#### IPv4 vs IPv6

Neither offer net-positive pro-privacy solutions.

You really just need to get better understanding your online surface area and
potential behavioral vulnerabilities.

Simple things go a long way:

* Use HTTPS whenever you can
* Additionally use a trusted or reputable logless VPN
* Use a restrictive SOCKS proxy (that you control) to avoid leaking
  information to your ISP

---
class: middle

#### DNS

You can leak quite revealing information about your web surfing and communication
habits by your DNS queries.

Direct links were found by security researchers BEFORE the election between
the Trump organization and a Russian bank. See:
http://www.slate.com/articles/news_and_politics/cover_story/2016/10/was_a_server_registered_to_the_trump_organization_communicating_with_russia.html

---
class: middle

#### DNS

In case you don't know:

* Any time your web browser or any of your networked software needs to translate
  (usually called _resolve_) hostnames into IP addresses (either IPv4 or IPv6),
  a DNS query will be issued (unless the software caches IPs, usually preferred
  to have the OS have a host-level cache, but not per-application).

* DNS queries are in the clear. Anyone on the local network could potentially
  see what queries you are making. That means if you are looking up the IPs for
  `www.rodneydavis.sucks` then you might determine they really hate the GOP
  US Representative for Illinois' 13th District. Just maybe.

---
class: middle

#### DNS

What can you do about this?

* Run DNS crypt proxy locally and choose a logless public resolver to query
  against: https://dnscrypt.org/
* Always use a VPN for all traffic, for all public IPv4 and IPv6 interfaces
  on your computer. *TODO: Jason :)*

---
class: middle

#### Web browsing hygiene (HTTPS, understand certs, ...)

* Install HTTPS Everywhere: https://www.eff.org/https-everywhere
* Install Privacy Badger: https://www.eff.org/privacybadger
* Test leak exposure via Panopticlick: https://panopticlick.eff.org/

---
class: middle

#### Identity system level threats (OpenSSL is a plague of insecurity built into everything:( )

Keep OS, system utilities, agents, services etc up to date (weekly? monthly?
unless critical security upgrade, in which case drop everything):

* OpenSSL, LibreSSL - ensure your gems use your patched Homebrew OpenSSL,
  LibreSSL not the Apple XCode version by passing appropriate gem install
  compiler options to bundler. For example,

```bash
bundle config build.puma -- --with-cppflags="-I$(brew --prefix openssl)/include -L$(brew --prefix openssl)/lib"
```

* Java (hopefully not using it at all?)
* cURL
* All your web browsers (unless you need them for QA/testing)

---
class: middle

#### Stay updated (firmware, OS, browsers)

* Subscribe to Apple's software updates notifications
* Review installed apps and updates in App Store regularly
* Audit Homebrew versions (`brew doctor`, `brew outdated`)
