# npm-dns-sync
A script to synchronise DNS entries between Nginx Proxy Manager and private/public DNS servers
# Contents
 - [What Is It](#what-is-it)?
 - [Supported DNS Providers](#supported-dns-providers)
	 - [Internal](#internal-dns)
		 - Microsoft DNS
	 - [External](#external-dns)
		 - Cloudflare
 - [How Do I Set It Up](#how-to-setup)?
 - [How Can I Contribute](#how-to-contribute)?

## <a name="what-is-it">What Is It?

A relatively simple bash script which scans the .conf files created by [Nginx Proxy Manager](https://github.com/jc21/nginx-proxy-manager) and adds or deletes dns entries to/from [supported DNS providers](#supported-dns-providers).

The script was borne out of frustration of having to manually update the DNS entries in my internal Active Directory domain as well as my external Cloudflare account. There had to be a better way and so this script came to be.

On each run of the script (after the first run where everything is considered "new") the script compares the previous run and the current run and works out which hosts have been added and which have been deleted - it them makes the appropriate calls to the DNS servers (using either nsupdate or 

## <a name="supported-dns-providers">Which DNS Providers Are Supported?
### <a name="internal-dns">Internal DNS
Essentially any DNS provide running on your local network which allows unauthenticated updates via the nsupdate command are supported. Yes, I know it's not secure, but for internal use and appropriate firewall rules it does the job for me.

I run an Active Directory domain at home so use Microsoft DNS and this script works fine there.

### <a name="external-dns"> External DNS

 - [Cloudflare](https://www.cloudflare.com)
 
## <a name="how-to-setup">How Do I Set It Up?
 - Clone the reporistory somewhere on the same host running Nginx Proxy Manager
 - Check you have the required applications installed on the host, at a minimum this script requires;
	 - bash
	 - curl (*sudo apt install -y curl*)
	 - diff (*sudo apt install -y difutils*)
	 - jq (*sudo apt install -y jq*)
	 - nsupdate (*sudo apt install -y dnsutils*)
 - Edit the script and set the required variables in the config section
 - Run the script
 - (Recommended): Add the script to cron to automatically pick up changes (I run the script every minute and changes are replicated in my Microsoft DNS and Cloudflare accounts quickly and reliably).
	
The following requires configuring per your install:
	- The interface name of the ipaddr parameter (e.g. ens19)
	- CONFIG_DIR - must point to the proxy_host directory of your Nginx Proxy Manager data
	- CNAME - if you want your internal DNS entries to point to a single CNAME, set this value, otherwise leave it blank and ipaddr (the IP address of the npm host) will be used
	- INTERNALDNS - set to 0 to disable internal DNS updates or 1 to enable it
	- EXTERNALDNS - set to 0 to disable internal DNS updates or 1 to enable it
	- CF_API_KEY - your Cloudflare API key, create one via your control panel
	- CF_ZONE_ID - the zone for which updates will be made, this can be found from your account profile
	- CF_IP_ADDR - the IP address to which Cloudflare entries will point
	- CF_RECORD_TYPE - what type of record to create (default A)
	- CF_TTL - the TTL of the DNS entries, set to 1 for 'Auto'
	- CF_PROXIED - set to true to have Cloudflare proxy your connections or false to enable direct connections

On first run, everything will be deemed as "new" so you may receive errors if the script tries to create DNS entries which already exist - you can safely ignore these. I may improve the script at some point to check each entry on the server before calling the add function but it's not on my list of priorities.

## <a name="how-to-contribute"> How Can I Contribute?
If there is a DNS provider you'd like to see supported, feel free to amend the script and submit a pull request - it would be great to add new providers.
