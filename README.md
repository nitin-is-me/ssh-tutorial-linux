### Introduction ###

This is an SSH tutorial I created for working on machines remotely (without port forwarding). I basically wanted to connect to my friend's WSL terminal from my pc so we can code on neovim together.
<hr>

## Step 1: Install Tailscale (Both of You)
#### 1. You (Client):<br>
```
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
Log in with your account (e.g., Google, Github).<br>
#### 2. Friend (Host):<br>
Same install steps, but logs into their own Tailscale account.

## Step 2: Share Access Securely
### Method A: Share a Single Device (Easiest)
#### Friend (Host):
1. Go to Tailscale Admin Console (https://login.tailscale.com/admin/machines).
2. Find your machine → Click "Share...".
3. Enter your host's (the one wanting access to your machine) email → They’ll get an invite link.
4. After accepting the invitation, the host has access to machine (not yet).

### Method B: Share a Tag (Advanced)
#### Friend (Host):
1. Go to Tailscale ACLs Config Page (https://login.tailscale.com/admin/acls).
2. Edit your Tailscale ACLs (https://login.tailscale.com/admin/acls):
```
{
  "tagOwners": {
    "tag:collab": ["emailofhost@email.com"]
  },
  "acls": [
    {
      "action": "accept",
      "src": ["tag:collab"],
      "dst": ["*:*"]
    }
  ]
}
```
3. Tag your device:
```
sudo tailscale up --advertise-tags=tag:collab
```
4. Now you’ll be able to access the device tagged tag:collab.

## Step 3: Verify Access
#### You (Client):
1. You run:
```
tailscale status
```
2. You should see your friend's device in the list:
    - If using Method A → `shared by friend@example.com `
    - If using Method B → `tags:tag:collab`   

## Step 4: Connect via SSH
#### You (Client):
1. From your machine, use the Tailscale IP shown in the status list or in Tailscale Admin Console.
1. Connect using your friend's Tailscale IP and machine username (only works after sharing and they should have tailscale server running using `sudo tailscale up`):
```
ssh friend-username@100.x.y.z
```

## Step 5: Start TMux Session (To share the same screen for collaboration):
#### Friend (Host):
```
tmux new -s collab
nvim file.txt
```

### You (Client):
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
