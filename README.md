# linux-firewall-recipes

Describe what a host should answer in one line; `fw.sh` applies it with iptables, nftables, ufw or firewalld.

A recipe is `ALLOW="port/proto[@cidr] ..."` plus `PING=yes|no`. Policy is always inbound-drop-except-allowed, outbound open, v4 + v6, persisted. `-n` prints the exact commands or nftables ruleset instead of applying, so it doubles as a generator.

## Runbook

```bash
git clone https://github.com/coffeestained/linux-firewall-recipes && cd linux-firewall-recipes

./fw.sh recipes/web-server.conf -t nftables -n   # look first
sudo ./fw.sh recipes/web-server.conf             # apply with whatever this box has (ufw > firewalld > nftables > iptables)
sudo ./fw.sh recipes/steam-server.conf -t ufw    # force a backend

# your own
printf 'ALLOW="22/tcp@10.0.0.0/8 8443/tcp 51820/udp"\nPING=yes\n' > recipes/vpn-box.conf
sudo ./fw.sh recipes/vpn-box.conf

# undo
sudo ufw --force reset                                       # ufw
sudo firewall-cmd --set-default-zone=public                  # firewalld
sudo nft flush ruleset                                       # nftables
sudo iptables -P INPUT ACCEPT; sudo iptables -F              # iptables (+ ip6tables)
```

## Recipes

| file | opens |
|---|---|
| `web-server.conf` | 22, 80, 443 |
| `steam-server.conf` | 22, Steam 27015-27030 + 4380, game ports (Zomboid by default) |
| `ssh-allowlist.conf` | 22 from one v4 and one v6 CIDR, ping off |

Just need SSH? [ssh-only-configurations](https://github.com/coffeestained/ssh-only-configurations) has hand-written, readable versions for each backend.

MIT © Matthew Grady
