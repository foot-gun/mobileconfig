# A reasonably paranoid mobileconfig for iOS
This mobile profile is provided as a skeleton to modify and apply to an iOS device with Configurator 2. It contains all restrictions that I recommend, plus placeholders for an always-on IKEv2 VPN connection and URL filtering enabled.

## Always-on VPN
The only type of VPN compatible with always-on is IKEv2. I am a ProtonVPN user, and [they support IKEv2](https://protonvpn.com/support/protonvpn-ios-manual-ikev2-vpn-setup/) with some tweaking.

If you are also a ProtonVPN user, just replace `REDACTED` in the configs with your provided OpenVPN credentials. If you wish, replace the IP addresses with the IP addresses of other servers from the Server Configs tab in the Downloads section (NB: do not use the Secure configs, they are not compatible with IKEv2).

If you are a customer of a different VPN that supports IKEv2, or you rolled your own (good on you!), don't forget to swap the CA payload with your provider's.
