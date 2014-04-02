# Current state of the Internet in Turkey

While browsing Twitter, I read a lot of confused Turkish internet users spreading wrong information about the current state of the Turkish Internet and the implications of the censorship techniques that are being used.

## DNS Filtering

When the Turkish government ordered Turk Telecom to start blocking Twitter, this is the first kind of blocking they started putting in place. You can think of *DNS* as the phone book of the Internet, for every name, you have a matching number that will be used for the routing (finding its path from source to destination) of the information.

Hence, when I type `www.twitter.com` in my navigation bar, my computer will first ask my DNS server to *resolve* (finding) the IP address that belongs to Twitter.

When my computer connects to the Internet, it is assigned an IP address and given local network settings, amongst them the address of a DNS server. This DNS server will be used as default to resolve queries unless you specify otherwise manually.

Let’s now take a concrete example. 

If a computer connects on a Turkish Internet connection, the above DNS lookup for Twitter will likely look like this.

```
~ ❯❯❯ dig www.twitter.com @195.175.39.39

; <<>> DiG 9.8.3-P1 <<>> www.twitter.com @195.175.39.39
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 33890
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

;; QUESTION SECTION:
;www.twitter.com.		IN	A

;; ANSWER SECTION:
www.twitter.com.	86400	IN	A	195.175.254.2

;; AUTHORITY SECTION:
twitter.com.		86400	IN	NS	ns.twitter.com.

;; ADDITIONAL SECTION:
ns.twitter.com.		86400	IN	A	195.175.254.2

;; Query time: 67 msec
;; SERVER: 195.175.39.39#53(195.175.39.39)
;; WHEN: Fri Mar 28 16:33:00 2014
;; MSG SIZE  rcvd: 82
```

Instead of looking like this (non-biassed answer from a Google Public DNS server).

```
~ ❯❯❯ dig www.twitter.com @8.8.8.8

; <<>> DiG 9.8.3-P1 <<>> www.twitter.com @8.8.8.8
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45726
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;www.twitter.com.		IN	A

;; ANSWER SECTION:
www.twitter.com.	587	IN	CNAME	twitter.com.
twitter.com.		17	IN	A	199.16.156.38
twitter.com.		17	IN	A	199.16.156.230
twitter.com.		17	IN	A	199.16.156.70

;; Query time: 11 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Fri Mar 28 16:34:55 2014
;; MSG SIZE  rcvd: 95
```

If you compare the records you will notice that different IP addresses are returned in the `ANSWER` section. The turkish DNS server responds with `195.175.254.2` while the Google DNS server responds with a few of Twitter’s official IP addresses. 

You can easily verify who registered an IP address thanks to a `WHOIS` lookup. Here are the results for the turkish DNS-returned IP address:

```
~ ❯❯❯ whois 195.175.254.2
inetnum:        195.174.0.0 - 195.175.255.255
netname:        TR-TELEKOM-960902
descr:          Turk Telekomunikasyon Anonim Sirketi
country:        TR
org:            ORG-TT3-RIPE
admin-c:        TTBA1-RIPE
tech-c:         TTBA1-RIPE
status:         ALLOCATED PA
remarks:        To report abuse problems mail abuse@ttnet.com.tr
remarks:        To report abuse problems mail abuse@ttnet.com.tr
mnt-by:         RIPE-NCC-HM-MNT
mnt-routes:     AS9121-MNT
mnt-domains:    AS9121-MNT
source:         RIPE # Filtered

organisation:   ORG-TT3-RIPE
org-name:       Turk Telekomunikasyon Anonim Sirketi
org-type:       LIR
address:        Turk Telekom Genel Mudurlugu
address:        Nazan OZTEKIN
address:        Turgut Ozal Bulvari Turk Telekom Genel Mudurlugu B Blok No308 Aydinlikevler Altindag
address:        06103
address:        Ankara
address:        TURKEY
phone:          +903123136282
fax-no:         +903123136589
admin-c:        SO351-RIPE
admin-c:        NK1283-RIPE
admin-c:        ZA66-RIPE
admin-c:        NO638-RIPE
admin-c:        OM2213-RIPE
mnt-ref:        AS9121-MNT
mnt-ref:        RIPE-NCC-HM-MNT
mnt-by:         RIPE-NCC-HM-MNT
abuse-mailbox:  abuse@ttnet.com.tr
abuse-c:        AR12859-RIPE
source:         RIPE # Filtered

role:           TT Administrative Contact Role
address:        Turk Telekom Genel Mudurlugu
phone:          +90 312 555 1920
fax-no:         +90 312 313 1924
admin-c:        BADB3-RIPE
abuse-mailbox:  abuse@ttnet.com.tr
tech-c:         NO638-RIPE
tech-c:         SO351-RIPE
tech-c:         MBD25-RIPE
nic-hdl:        TTBA1-RIPE
mnt-by:         AS9121-MNT
source:         RIPE # Filtered

% Information related to '195.175.192.0/18AS9121'

route:          195.175.192.0/18
descr:          TTnetTurkTelekom
origin:         AS9121
```

Further readings about the DNS system: 


Erdogan’s first block was to replace those results with an IP address that belonged to TurkTelecom and that displayed this page: 

/// 


The good news about this kind of censorship is that changing it is really easy. Removing someone from the phone book doesn’t prevent you from calling him, it will of course be more difficult for most people to find out where that person is on the network but they will figure it out, and once they have the number they can reach him. 

Pictures of Turks changing DNS and spreading 8.8.8.8



## IP-Blocking 

As we’ve seen in the previous section, DNS blocking is easily circumventable (but let’s not forget that it successfully prevents the less techies to access their favourite blocked website).

Now we’ve seen that removing a number from the phone book doesn’t prevent that number to be reached if you have it. IP-level blocking is just that. How it appears to be implemented is that their routing algorithms, the code that controls how the data packets are transferred from one router to another have been changed so that the packets going to those IP addresses go nowhere and are dropped. 

How can this be circumvented? Instead of trying to reach Twitter, or our host directly, we will reach one or more third party hosts that will forward our traffic. from the source 

In this block post we’ll cover two circumvention tools that can be used against this kind of blocking.

# VPN

VPN, standing for Virtual Private Network will achieve exactly this task. The VPN software will create a so called “VPN tunnel” between your host and the VPN server. That runner will be used to transfer all the traffic from the server 


Because the censor, in this case Turkey, doesn’t have the encryption keys for your VPN tunnel, it can’t block specific sites. They can use Deep packet inspection to slow down VPNs or shut down VPNs completely but they won’t be able to block only Twitter on your VPN.

Note: VPNs don’t provide anonymity. Your VPN operator knows exactly what websites you are visiting and can snoop on unencrypted traffic. Thus using HTTPS when possible is always recommended
 

## Tor 

The other widely used alternative to IP blocking is to use the Tor network. Tor is a robust free solution for anonymity. 

How does Tor work?

When you open Tor, it fetches a list of relays from which it will choose 2 from and a list of exit nodes from which it will choose one. It will then proceed to establishing a *circuit* from your machine to the exit node. The exit node, won’t know your real IP address, only the first relay will have that information. 

Now if you use the Tor Browser Bundle to connect to Twitter.com, the request will be forwarded through your Tor circuit where the exit nodes transmits your TCP packets. 


 

## Deep Packet Inspection



## About Tor

## About VPNs

## Preventing eavesdropping

Whether you use a VPN or Tor, your unencrypted traffic can be snooped on. That’s why we recommend you to take two extra measures. 

Certificate pinning in the Tor Browser Bundle, technical enough, just use Chromium. 


HTTPS everywhere. 

DNS ecnrypt 

## QA

did they block Tor?



differences in threats to anon. and to availability (blocking entry and/or exit nodes?) 

What data is able to be gleaned about potential equipment manufacturers being used to implement it and use of LI equipment?