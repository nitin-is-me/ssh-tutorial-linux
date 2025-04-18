### Introduction ###

This is an SSH tutorial I created for working on machines remotely (without port forwarding). I basically wanted to connect to my friend's WSL terminal from my pc so we can code on neovim together.
<hr>

## Step 1: Install Tailscale (Both of You)
### 1. You (Host):<br>
```
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
Log in with your account (e.g., Google).<br>
### 2. Friend (Client):<br>
Same install steps, but logs into their own Tailscale account.

## Step 2: Share Access Securely
### Method A: Share a Single Device (Easiest)
1. Go to Tailscale Admin Console (https://login.tailscale.com/admin/machines).
2. Find your machine → Click "Share...".
3. Enter your friend’s email → They’ll get an invite link.

### Method B: Share a Tag (Advanced)
1. Edit your Tailscale ACLs (https://login.tailscale.com/admin/acls):
```
{
  "tagOwners": {
    "tag:collab": ["your@email.com"],
  },
  "acls": [
    {"action": "accept", "src": ["tag:collab"], "dst": ["*:*"]},
  ]
}
```
2. Tag your device:
```
sudo tailscale up --advertise-tags=tag:collab
```

## Step 3: Verify Access
Your friend runs:
```
tailscale status
```
Should see your device with `tags:tag:collab` (or shared explicitly).

## Step 4: Connect via SSH
Friend connects using your Tailscale IP (only works after sharing):
```
ssh your-username@100.x.y.z
```

## Step 5: Start TMux Session
### You (Host):
```
tmux new -s collab
nvim file.txt
```

### Friend attaches:
```
tmux attach -t collab
```

Finished!


## Why tailscale
It creates a peer to peer connection between devices, so it behaves like all the connected devices are on same LAN network, without needing any port forwarding. Tailscale can be used for multiple purposes, like:
1. Access Home Lab Servers Securely
2. Play LAN Games Remotely
3. Access Company Server/Database from Home
4. Share Files Instantly
5. Control Smart Home Devices which need LAN
6. Remote Desktop
7. Secure Cloud Server Access
and many more...
