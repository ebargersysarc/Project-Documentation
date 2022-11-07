FIPS-CC mode overview

FIPS-CC mode enforces the firewall to use FIPS compliant features and encryption. There ARE specific version that have been "FIPS validated" but they are usually very far behind the current available firmware revisions. You don't have to be on one of those FW versions to enable FIPS-CC.

The documentation from Fortigate on EXACTLY what turning this on does, is non-existent. (Even their support engineers don't seem entirely sure of everything it does.)

Here is a few things to note when enabling FIPS-CC:

-   Must be done via console connection
-   It wipes MOST of the current config
-   You cannot restore from a non-FIPS config
-   It forces the Fortigate to do a FIPS checklist upon boot
-   Any FortiAPs BELOW the "E" series are NOT compatible (Letters indicate series in order of oldest to newest i.e. FAP221A, FAP221B, FAP221C, etc.)
-   Many cryptographic functions will be removed as options when creating IPsec tunnels (such as MD5 and SHA1)
-   Interfaces are disabled by default when being created
-   Firewall policies are disabled by default when being created

-   After enabling FIPS-CC the Fortigate will ask you to set a new administrator password. EVERY time I have done this, the Fortigate says it accepts the new password, but does not accept it upon reboot. The old password still worked for me in all cases.

commands:

config system fips-cc
	set status enable
	end