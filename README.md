Tihs is an attempt to make the broken Radio Configuration Utility work again. 

I decompiled the original, patched the source to change how it checked to see if the changes to the host computers IP address had worked and recompiled.  I looked for the original source but I couldn't find it anywhere, so I took the ugly route.

I decompiled using Vineflower

I edited and recompiled using Intillij community edition. 

To use, simply copy the modified FRCWpaKiosk.jar over the original in the C:\Program Files (x86)\FRC Radio Configuration Utility\lib folder. 

Hopefully this helps some people.  My anger and frustration over trying to make the broken tool work will hopefully be at an end. 

This is a quick and dirty fix and I don't really know what I'm doing so use at your own risk, but so far it works for me.


The entirety of the fix is in how getLocalhostAddresses works.  Now it simply iterates through all interfaces gathering all addresses instead of trying and failing to get only the addresses of the selected interface.  The problem ended up being that there were many interfaces that matched the selected interface name and mac address, most of which were inactive and did not get the addresses when configured.  The code would only check the first one that matched.  Because of this, while the reconfiguration command worked properly, the check to see if the addresses were configured was usually checking an inactive interface which would never get configured. This is the new code for generating a list of local IPs:

         List<InetAddress> ips = new ArrayList<>();
         for (NetworkInterface nic : buildNicList()) {
            List<InterfaceAddress> addrs = nic.getInterfaceAddresses();

            if (addrs.size() > 0 ) {
               logger.debug("Getting addresses for nic: " + nic.getDisplayName() +  " index " + nic.getIndex() + " : " + addrs.size());
               ips.addAll( Collections.list(nic.getInetAddresses()) );

            }
         }
         return ips;
         
-Eric Garland 
FRC 8708 Programming Mentor
