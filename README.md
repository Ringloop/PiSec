# PiSec
A lightweight, efficiency-oriented phishing protection system.

## Introduction
PiSec is a network security tool entirely implemented in Go that provides active protection from phishing, blocking the navigation to a suspectedly malicious link.

Phishing is one of the most used attacks in Computer Security: It occurs when an attacker tricks a user into handing over sensitive data such as passwords, credit card numbers and so on.
It usually begins with an e-mail containing a URL leading to a malicious website that closely resembles an authoritative one.

PiSec checks if the URL is a malicious one, blocking traffic if so. The approach we followed is alternative to other existing solutions (such as PiHole, https://pi-hole.net/) since we used an HTTP proxy to control navigation instead of a DNS server.
This kind of approach presented above, presents some pros and cons:

- we introduce a little bit of performance penalty, due to the proxy forwarding.
- this approach let us the possibility to customize the page in case of http / https and then forwarding to original site, in this way always leave the possibility to the user to navigate to the original destination in case he/she knows where is going.
- moreover, this kind of approach gives us the possibility to perform a deeper traffic analysis in the future, right now we analyze only the contacted domain name but in future we could analyze the amount of data transmitted, payloads, etc...

## Implementation and application architecture

Below the application domanin (PiSec Cloud) architecture: 

![image](https://user-images.githubusercontent.com/7256185/188719488-7e6addf1-58a1-4823-8114-f892a8845b74.png)

Our implementation is based on a central cloud knowledge approach: we have a central server (https://github.com/Ringloop/PiSec-Brain) storing the information, this server exposes APIs (in form of REST APIs) to interact with other components. 
The central database we chose is ElasticSearch (https://www.elastic.co/), a NoSQL document store. Our choose has been driven by two main reasons: 

- there is a rich ecosystem in Elastic stack for creating charts and provide data visualization through  Kibana and other out-of-the-box integrations

- elasticsearch give us the possibility, for the future, to implement a fuzzy search to find similarities on the domains and implement complex logic on the malicious link recognition.

The main database, providing knowledge about malicious urls, is powered by multiple crawlers (https://github.com/Ringloop/PiSec-Crawler/), periodically gathering informations from multiple sources and ensuring that the knowledge is always updated. At the moment, we are retrieving informations about malicious links (URLs, IP addresses and other metadata) from two known sources: PhishTank (https://phishtank.org/) and PhishStats (https://phishstats.info/). Each crawler is a standalone application, so other crawlers are possible in the future.

The customer logic (we mean: the real control over user navigation) is implemented through a proxy (https://github.com/Ringloop/PiSec-Proxy) that should be installed locally for each customer. Our effort is to ensure that this proxy is tiny and lightweight, so that it can be installed also on a limited machine, such as a Raspberry Pi, or other general purposes SOCs.

We used some measures in order to minimize the requirements for the proxy: 

![image](https://user-images.githubusercontent.com/7256185/188719539-b9a7f25e-ed16-472a-afaa-cf5b47de941e.png)

- We used Bloom Filters (https://en.wikipedia.org/wiki/Bloom_filter) to store the entire setof malicious links is downloaded from the server in order to minimize space occupation and maximize efficiency in search. 

- Since Bloom Filters does not provide any assurance about the presence of sought data, in case of positive match, a confirmation from the server is needed. We implemented a caching mechanism leveraged by Redis (https://redis.io/), so that we keep a list of confirmed malicious links, a list of confirmed false positive and a list of links confirmed by the user, that is, those links confirmed to be registered as malicious, but user has confirmed he wants to visit. 

## Future work

This project is continously growing and we are open to receive support from the Open Source and GitHub community. Some ideas for the immediate future development is: 

- shard data in multiple bloom filters to implement incremental update and retention on client side

- optimize and prepare the bloom filters before the clients request them, maybe using timers. This allow to optimize the query on ElasticSearch since many documents are read for this operation.

- implement and perform stress test, performance and latency measurement to provide statistics on memory usage


