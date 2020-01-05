---
date: 2017-01-11
layout: post
title: "How SSH authentication works"
...

A great friend of mine, [Diego "Diegão" Guimarães][diegao] (which also happens to be one of the best programmers I ever met), recently asked me: "why do I have to specify the private key when connecting to an SSH server and not the public one?". I found this question quite interesting, as it reminds us that even seasoned developers may have doubts about things they use every day. And, of course, it's always better to ask than accept that "this is just the way it works".

Before explaining how SSH authentication is performed, we have to understand how public key cryptography (also known as _asymmetric cryptography_) takes part in this process. Worth mentioning that SSH authentication can also be done using passwords (and this usually is the default setting), but this won't be discussed here. If you have a machine where its SSH server accepts passwords as an authentication method, [you should probably disable this anyway][disable-ssh-password-auth].

Public key cryptography works with pairs of keys. A public one, like its name suggests, can be known by anyone. It can be sent to an internet forum or be published as part of a blog post. The only thing you have to worry when facing a public key is that if its owner is really the same person who they affirm to be. Its trustworthiness relies on the fact that no one can impersonate the key owner, i.e., have the private counterpart of a public key pair - but anyone can generate a key and tell you that it's from someone else.

But how can you be sure about who is the owner looking at the key itself? The problem is that you can't. A public (Ed25519) key looks like this:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAII+kwSB7I6nTJ+mqhNMlEzofxa4UaGsU09A8Yk95MU1n tiago@ilieve.org
```

This format is pretty straightforward, based on `{key algorithm} {base64 representation of the key itself} {comment}`. The problem lies in the fact that pairs of keys can be easily generated with `ssh-keygen`. The "comment" part can even be altered after it was created. You can only be sure about who is the owner by the way you got a public key from someone else. Did they sent it to you via e-mail? Can you call them to confirm that this is the right key? Did you got it in a thumb drive brought to you personally by themselves?

The core functionality of this whole system is that a piece of information encrypted with a public key can only be decrypted by its private key. The opposite is true as well, as this is how [signing][signing] works. Based on this cryptographic principle, the authentication process of an SSH connection works (in a simplified view) as follows:

* The client sends an authentication request, informing the username that will be used to log in.
* The server responds, telling the client which authentication methods are accepted (e.g. "publickey", in this case).
* A message encrypted with the private key (a "signature") is sent by the client to the server along with its corresponding public key.
* If this public key is listed as acceptable for authentication (usually as an entry under `~/.ssh/authorized_keys`) and the signature is correct, the process is considered successful.

It's important to point out that the only way one can generate a valid signature to be checked with a public key is by having the private key itself. That's the very reason why private keys shouldn't be shared in any way, even with people you trust. That's no need for two or more persons to have access to the same private key. Everyone should have its own and the server should be configured to accept all of them.

We should be aware that there's a case where the user can be confused about whether the authentication is being done using passwords or public keys: when the private key has a passphrase. To safely store a key, a passphrase can be set to save it in a (symmetric) encrypted format. This also works as another layer of security, as someone who happens to obtain the keyfile would also have to know the passphrase to unlock it. In this situation, [ssh-agent can be used to cache this information][ssh-agent] in order to avoid typing it every time.

References:

* [RFC 4252 - The Secure Shell (SSH) Authentication Protocol][rfc-4252]

[diegao]: https://github.com/diegoguimaraes
[disable-ssh-password-auth]: https://help.ubuntu.com/community/SSH/OpenSSH/Configuring#Disable_Password_Authentication
[rfc-4252]: https://tools.ietf.org/html/rfc4252
[signing]: https://en.wikipedia.org/wiki/Digital_signature
[ssh-agent]: https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#adding-your-ssh-key-to-the-ssh-agent
