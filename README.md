# PiSec
A lightweight, efficiency-oriented anti phishing tool

![image](https://user-images.githubusercontent.com/7256185/188719539-b9a7f25e-ed16-472a-afaa-cf5b47de941e.png)


PiSec is a network security tool that provides active protection from phishing, blocking the navigation to a suspectedly malicious link.


Phishing is one of the most used attacks in Computer Security: It occurs when an attacker tricks a user into handing over sensitive data such as passwords, credit card numbers and so on.
It usually begins with an e-mail containing a URL leading to a malicious website that closely resembles an authoritative one.

PiSec checks if the URL is a malicious one, blocking traffic if so. 
The implementation is based on a mixed approach, leveraging a proxy installed on the user's local network and a server, deployed in the cloud. The proxy and the server communicate through a REST API.

While the cloud server manages the entire set of URLs considered malicious, the proxy manages the user connectivity.
The proxy is a lightweight component that should run on minimalistic hardware (such as a Raspberry Pi) and should provide high performance with minimal impact on network bandwidth and latency.

![image](https://user-images.githubusercontent.com/7256185/188719488-7e6addf1-58a1-4823-8114-f892a8845b74.png)

This project is composed by 3 main components, present in this repository as submodules:

- PiSec Brain: the main server that manages the central repository of URLs considered malicious. The storage layer is implemented using Elasticsearch (https://www.elastic.co/).

- PiSec Crawlers: multiple instances of a stand alone application that retrieves a list of URL, IP addresses and other metadata from known sources (e.g. PhishTank https://phishtank.org/ and PhishStats https://phishstats.info/). The data is used to feed the Brain component.

- PiSec Proxy: Proxy: an application that intercepts each HTTP connection request, and if the destination URL matches one of the URLs in the central repository, blocks it. The implementation is based on goproxy (https://github.com/elazarl/goproxy).
The membership test for the URL is carried out by a Bloom Filter, in order to maintain high performance with a small memory footprint. To further reduce the latency, the responses from the Brain are cached using Redis (https://redis.io/). 




Info to add / merge:



## Pisec is a light phishing protection system.
- All implemented in golang
- Alternative approach to piHole, since here we are using an http proxy instead of a dns server:
- pros and cons of this approach:
- cons 1: little bit of performance penalty, due to the proxy forwarding
- pros 1: possibility to customize the page in case of http and then forwarding to original site
- pros 2: possibility to perform more analysis in the future, right now we analyze only the contacted domain name but in future we could analyze the amount
of data transmitted, payloads, etc...


## Key concept: KISS + Hobby probject

- there is a central cloud knowledge: multiple  crawlers, and an api exposing data stored in elasticsearch
- there is a local proxy for each customer (possibility to install it on a rasberry pi, but non limited only to rasberry pi)
- the cloud database is shared with the customers using a succint data structure (bloom filter)

we chose Elastisearch as a no sql document store for two reasons:
- 1: rich ecosystem for creating charts and visualization (kibana)
- 2: in future we would like to use the fuzzy search in order to find similarities on the domains



## Future improvements
- shard data in multiple bloom filters in order to implement retention on client side
- optimize and prepare the bloom filters before the clients request them, since many documents in Elasticserarch are read for this operation
- Perform stress test and stats on memory usage
