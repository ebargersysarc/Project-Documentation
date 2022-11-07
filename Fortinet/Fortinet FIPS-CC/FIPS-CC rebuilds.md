When doing a fips-cc rebuild of a Fortinet, and you want to restore the previous configuration, you will need to download a copy of the config and prepare a new version to be 'uploaded' back in to it after enabling FIPS-CC mode.

it is VERY important to re-apply the various parts of the config in the correct order, as some items have dependencies on others such as Firewall rules need interfaces. I like to create several smaller txt files and copy out the parts I need and name them accordingly. Here is an example of the parts I would likely extract in no particular order:

-   Global (config system global & config system settings)
-   DNS (config system dns)
-   SNMP (config system snmp sysinfo & config system snmp community)
-   Admin Accounts (config system admin)
-   Service Objects  (config firewall internet-service-name)
-   Interfaces (config system interface)
-   Wireless (config wireless-controller vap)
-   DHCP (config system dhcp server)
-   Address Objects (config firewall address, config firewall addrgrp, config firewall service custom, config firewall service group, config firewall service category, config firewall wildcard-fqdn custom)
-   RADIUS (config user radius)
-   LDAP
-   Users (config user adgrp)
-   VPN (config vpn ssl settings, config vpn ipsec phase1-interface, config vpn ipsec phase2-interface)
-   Firewall Policies (config firewall policy)
-   Static Routes (config router static)
-   HA (config system ha)

As for the order of these parts. This will vary for every project but here is a good general order to follow:

Global, admin accounts, interfaces, DHCP, service objects, address objects, static routes. Firewall policies almost always come last.

Many parts of the config will have previous information that you need to get rid of such as anything with UUID (this is a unique value assigned automatically when creating the object and you cannot change this value)

I have found the most effective method to get rid of these things is to use regular expressions in Find & replace in Notepad++.

Here are some samples that I have used that may help you find and remove these certain objects

-   set uuid \w{8}-\w{4}-\w{4}-\w{4}-\w{12}
    this will find those lines with UUIDs so that you can remove them. just replace with nothing and it will remove the lines (don't worry about the extra space it leaves in the config)  
      
    You can also adjust the numbers in there to account for any size special character set if you need it to match a different format.  
     
-   \r\nset status up  
    this part is important for your interfaces, because as we discussed before, FIPS mode is going to disable all of your interfaces by default. I like to run this find & replace on something like  
    Find:  set vdom "root"  
    Replace with: set vdom "root"\r\nset status up  
    This will effectively just add a new line with the set status up after the vdom line. and should look like this  
     
    set vdom "root"
    set status up

-   To add on to the above, and interfaces you have with static Ips (such as your WAN) you will need to add "set mode static" in there as well. But be careful not to add this to everything. This is probably best suited to doing manually, as there shouldn't be too many interfaces with static addressing.

-   \r\nset status enable  
    Same thing as above but use this for your firewall policies. I like to find my "anchor" text as something like "set schedule "always"" which is a very common default action on FW policies.

Other parts of the config will have password hases saved and will look something like this:  
set passphrase ENC eAidH9I7xiuIOf2kXyi81D0QWptFKdUgi2skRurYg7anGLucG2ZoD8HKWZFLyoL04b10mWiyA04kYKrW5bfG73+rtITQ==

If you are restoring this config to the EXACT SAME Fortigate unit that you copied it from, then the hashes will continue to work. At many points of the "rebuild" it will ask you to confirm the passphrase. Simply copy&paste starting from the "ENC" and it will accept it.