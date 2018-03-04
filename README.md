# A reasonably paranoid mobileconfig for iOS
This mobile profile is provided as a skeleton to modify and apply to an iOS device with Configurator 2. It contains all restrictions that I recommend, plus placeholders for an always-on IKEv2 VPN connection and URL filtering enabled.

## Always-on VPN
The only type of VPN compatible with always-on is IKEv2. I am a ProtonVPN user, and [they support IKEv2](https://protonvpn.com/support/protonvpn-ios-manual-ikev2-vpn-setup/) with some tweaking.

If you are also a ProtonVPN user, just replace `REDACTED` in the configs with your provided OpenVPN credentials. If you wish, replace the IP addresses with the IP addresses of other servers from the Server Configs tab in the Downloads section (NB: do not use the Secure configs, they are not compatible with IKEv2).

If you are a customer of a different VPN that supports IKEv2, or you rolled your own (good on you!), don't forget to swap the CA payload with your provider's.

## Blacklisting ads and trackers with your own list
For convenience, I also utilized [notracking's host blocklist](https://github.com/notracking/hosts-blocklists) repo to block ads and trackers via the Content Filter. This is accomplished by extending the parental content control features, so be aware that you will have to add your favorite sketchy or adult sites to a whitelist. This blocks a static list of domains and cannot handle regular expressions within domains the way browser extensions can.

Use whatever list you like, or stick with the one I used and keep it up to date. The end result is a list of `BlacklistedURLs` compatible with Apple's [Configuration Profile Reference](https://developer.apple.com/library/content/featuredarticles/iPhoneConfigurationProfileRef/Introduction/Introduction.html). Technically, the list of URLs should be no more than about 500.

Start by cloning your repo of choice, and locate the files with your desired domains.
```
git clone https://github.com/notracking/hosts-blocklists.git
```
Since `sed` on OSX is not the same as Linux, we have to give it the `-E` flag to recognize regular expressions in the match, but it still won’t insert them for substitutions. To insert four tab indentations before `<string>`, press Control (not Command)+ V followed by the Tab key and repeat this combination three more times.
```
grep address hosts-blocklists/domains.txt | tr -d “/” | sed ‘s/0.0.0.0/\<\/string\>/g’ | sed -E 's/address=/ctrl+v+tab x4\<string\>/g' > blacklist.out
grep 0.0.0.0 hosts-blocklists/hostnames.txt | sed -E 's/0.0.0.0 /ctrl+v+tab x4\<string\>/g' | sed s/$/'\<\/string\>'/ >> blacklist.out
```
Now make a copy of the rest of the mobileconfig file, without the blacklisted URLs.
```
egrep -v "<string>http" ios.mobileconfig > new.mobileconfig
```
Copy the entire contents of the blacklist to your clipboard.
```
cat blacklist.out | pbcopy
```
Open the new mobileconfig file with your favorite text editor and find the string `BlacklistedURLs`. In the space between the `<array>`s below it, paste your long list of URLs to blacklist. Save it and apply it via Configurator!

### TODO
* Create an alternate whitelist-only lockdown config for apps and URLs, disable Safari.
* Determine if entire protocols can be disabled with Content Filtering by specifying alternate URI schemes.
* Test whether a huge blacklist (>>500) explodes.
