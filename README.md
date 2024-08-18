
# Proxmox VE Acme.sh DNS API script for Hurricane Electric Dynamic DNS records

Proxmox Virtual Edition (PVE) uses [acme.sh](https://github.com/acmesh-official/acme.sh) DNS API scripts
to interface with various DNS providers in order to automate the use of Let's Encrypt certificates.
Currently there is no built-in support for the per-record Dynamic DNS API of
[Hurricane Electric (HE) DNS service](https://dns.he.net/) in acme.sh.

This repository provides install script to add the support for `he_ddns` provider
in PVE.

## Instructions

1. Download and copy `install.sh` to the PVE host, for example in `/tmp`
1. Inspect the contents of the `install.sh` script (this is important!)
1. If you think the script is safe to run, run it as root user, like `bash /tmp/install.sh`
1. In the HE DNS portal (assuming you already have an A/AAAA record of `pvehostname.example.com` for your PVE node):
    1. Add TXT record for `_acme-challenge.pvehostname.example.com` in your DNS zone,
selecting "**Enable entry for dynamic dns**" option (this record will be dynamically updated
by the acme.sh script whenever renewing the certificate)
    1. Click the "**Generate a DDNS key**" button in the DDNS column, click the
"Generate a key" button in the form, copy the key and Submit the form
1. In PVE, in the Datacenter-level ACME configuration, add new Challenge Plugin:
    - Plugin ID: he-ddns
    - DNS API: select **HE DDNS** (this was added by the install script)
    - HE_DDNS_KEY: paste the key you generated for the TXT record above
1. Add an ACME Account in PVE
1. In the PVE node, in System - Certificates, add ACME domain:
    - Challenge Type: DNS
    - Plugin: he-ddns (the one you created at the Datacenter level above)
    - Domain: `pvehostname.example.com`
1. Above the ACME Domain list, select the ACME account to use
1. Click Order Certificates Now

## Notes

The `_acme-challenge` TXT record is not deleted automatically by the script as the script is
only able to update the record contents, not create or delete the record.
If you need the `_acme-challenge` TXT record to be deleted after renewing the certificate, use some other
acme.sh DNS API script.

This work is licensed under the GNU General Public License v3.0, you know, there is no warranty, etc.
