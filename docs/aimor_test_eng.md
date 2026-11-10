# DNS. Basic information

<div markdown>

## What is DNS

**DNS** (Domain Name System) is a system that stores data about domain names, including their IP addresses. It works like the internet's address book: it uses a website's name (e.g., `example.com`) to find its IP address (e.g., `93.184.216.34`).

Why DNS matters: users type human-readable website names into a browser, but devices find each other by IP addresses. Before every connection to a website, the device sends a **DNS query**—the question "what is the IP address of this domain?"—and receives an answer from a DNS server.

!!! note ""
    DNS also stores other types of data, such as mail server information. Read more about them in the article "[DNS records](link)".

## How DNS works

The whole process takes a fraction of a second. To avoid repeating it every time, the answers are temporarily stored in a **cache**.

**Step 1. Checking the device's cache**  
When a user enters a domain name and presses Enter, the browser checks the cache on the device:

- a match is found — the browser connects to the website using the IP address; 
- no match is found — the browser sends a request to the recursive DNS server.

**Step 2. [Recursive server (resolver)](#resolver)**  

The recursive DNS server checks if the answer is in its cache:

- a match is found — the resolver returns the IP address to the browser, and it navigates to it;
- not found — the resolver goes through a chain of servers (steps 3–5) to find the IP address.

**Step 3. Root server**  
The root server does not know the IP addresses of websites, but it knows which servers are responsible for top-level domains (`.com`, `.org`, `.ru`). It provides the address of the required TLD server. The resolver contacts the TLD server.

**Step 4. Top-level domain server (TLD server)**  
A TLD is a top-level domain, meaning the last part of the name: `.com`, `.org`, `.ru`. The TLD server knows which server stores the records for a specific domain and provides its address. The resolver contacts this server.  

!!! note ""
    TLDs come in several types: country-code, generic, sponsored, and others. For more details, see the article "[What is a TLD and what types are there](link)".

**Step 5. Authoritative DNS server for the domain**  

This server stores the up-to-date records of the domain entered by its owner. For a website, this is the record with the IP address.

**Step 6. Response and caching**  
If the domain's authoritative DNS server returns the website's IP address, the resolver saves it to the cache and passes it to the browser. The browser **establishes a connection** with the website using this IP address.  

!!! warning ""
    The IP address may not be found for several reasons:  
    
    - a typo in the domain name;
    - the domain does not exist or has expired;
    - an error in the DNS records settings; the authoritative server does not contain the required record for this domain;
    - the DNS server is unavailable or not responding.

    In this case, the user sees an error, such as "Site not found".

## Types of DNS servers
DNS servers differ in two ways: **the role** they perform and **who manages** them. The same server can fit multiple descriptions.

**By role**

| DNS server type | What it does | Where it is found |
| :- | :- | :- |
| Authoritative | Stores records for a specific domain and provides the final answer. It does not query other servers; it returns exactly what the domain owner specified | Hosting providers, domain registrars, website owners' DNS services |
| <a id="resolver"></a> Recursive (resolver) | Receives a request from a device. If the answer is not in the cache, it contacts the root, TLD, and authoritative servers until it gets the IP address | Providers, public DNS, VPN services, routers |
| Root and TLD servers | Indicate where to look for the next server in the chain. They do not store website addresses | Root server operators and TLD registries|

!!! note ""
    A **caching DNS server** is not a separate type but a function: saving received answers for the duration of the TTL (Time To Live — how long a record may be cached). It is present in most recursive servers, routers, and operating systems. The cache speeds up website loading and reduces the load on DNS servers, but it may return an outdated answer until the TTL expires.

**By ownership**

| DNS server type | Description | Examples |
| :- | :- | :- |
| ISP (Provider) | The recursive server of your internet service provider. Used by default | ISP's DNS server |
| Local | Operates within a home or work network or on the device itself. It can store answers in a cache and forward other requests to an upstream server | Wi-Fi router, DNS client service in the operating system |
| [Public](#public-dns-servers) | A recursive server available to any internet user | Google, Cloudflare, Quad9 |


## Public DNS servers 
By default, a user's device uses the provider's DNS servers, which may be slow or unstable. Public DNS servers allow you to:

- speed up website loading;
- improve security;
- bypass provider blocking if it is done at the DNS level;
- reduce dependence on the provider.

| Provider | Primary IP | Secondary IP | Feature |
| :--- | :--- | :--- | :--- |
| Google | `8.8.8.8` | `8.8.4.4` | High speed, stability, servers worldwide |
| Cloudflare | `1.1.1.1` | `1.0.0.1` | Privacy-focused, states that it stores minimal query data |
| OpenDNS | `208.67.222.222` | `208.67.220.220` | Parental controls and phishing protection |
| Yandex DNS | `77.88.8.8` | `77.88.8.1` | Optimized for users from Russia |
| Quad9 | `9.9.9.9` | `149.112.112.112` | Blocks known malicious domains |
| AdGuard DNS | `94.140.14.14` | `94.140.15.15` | Blocks ads and trackers at the DNS level |

!!! note ""
    The secondary IP address is used if the primary one is unavailable.

!!! warning ""
     When using a VPN, there is usually no need to change DNS servers in the operating system settings. Queries should go to the VPN client's DNS server (see more in the section ["How DNS and VPN are related"](#how-dns-and-vpn-are-related)).

## How DNS and VPN are related

VPN encrypts traffic and hides the real IP address. When visiting websites, the user still sends DNS queries, so it is important **where** they go.

Usually, a VPN client uses one of the following options:  

1. VPN's DNS server — a [resolver](#resolver) on the VPN service's side.
2. A public DNS server — for example, `1.1.1.1`.
3. User's device DNS servers — this option most often leads to a [DNS leak](#dns-leak).

|  | DNS query inside the VPN tunnel | DNS query outside the VPN tunnel |
| :- | :- | :- |
| Where it goes | To the DNS server used by the VPN | To the provider's or router's DNS server |
| What the provider sees | Only encrypted traffic to the VPN server | The list of domains the user requests |
| Result | As it should be | DNS leak |

### DNS leak  

A DNS leak occurs when the VPN is on, but DNS queries go outside the VPN tunnel. As a result, your ISP can still see which websites you visit.

To check for a DNS leak:

1. Turn off the VPN and visit any DNS leak testing service.
2. Note which DNS servers and country the service shows.
3. Turn on the VPN and test again: 
    - ISP or user's country appears in the results — there is a leak;
    - the VPN service or a different country appears in the results — there is no leak.

!!! note ""
    If you have trouble setting up DNS in the app, see "[DNS and VPN troubleshooting](link)".

</div>