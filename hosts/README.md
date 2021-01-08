## Host file for the Pi-Hole Gravity list to block domains.
### This file can also be used in any OS that supports host file to block domains.


### Block streaming music & video domains.
### Filename : online_streaming_domains
In case you want to add all possible sub-domains and directories for a specific domain name, then use **RegEx**. This can be added in the **Blacklist** =>**RegEx** in Pi-Hole's web interface or using the command line.  
Example : (^|.)spotify.com(|.$) will block spotify.com, www.spotify.com, open.spotify.com, www.spotify.com/be-fr/, guc-dealer.spotify.com   
Example : (^|.)domain(|.*).com(|.$) will block spotify.com, www.spotify.com, open.spotify.com, www.spotify.com/be-fr/, guc-dealer.spotify.com, sp-bootstrap.spotifycdn.com   
  
-*The list will be updated as and when I discover more domains*  

### Block malware domains.
### Filename : malware_domains
Original Source: https://mirror1.malwaredomains.com/files/justdomains
Original Source is no longer available, hence this file had been created.
