# PiSec
A lightweight, efficiency-oriented anti phishing tool

PiSec is a network security tool that provides active protection from phishing, blocking the navigation to a suspectedly malicious link.

Phishing is one of the most used attacks in Computer Security: It occurs when an attacker tricks a user into handing over sensitive data such as passwords, credit card numbers and so on.
It usually begins with an e-mail containing a URL leading to a malicious website that closely resembles an authoritative one.

PiSec checks if the URL is a malicious one, blocking traffic if so. 
The implementation is based on a mixed approach, leveraging a proxy installed on the user's local network and a server, deployed in the cloud. The proxy and the server communicate through a REST API.

While the cloud server manages the entire set of URLs considered malicious, the proxy manages the user connectivity.
The proxy is a lightweight component that should run on minimalistic hardware (such as a Raspberry Pi) and should provide high performance with minimal impact on network bandwidth and latency.

This project is composed by 3 main components, present in this repository as submodules:

- PiSec Brain: the main server that manages the central repository of URLs considered malicious. The storage layer is implemented using Elasticsearch (https://www.elastic.co/).

- PiSec Crawlers: multiple instances of a stand alone application that retrieves a list of URL, IP addresses and other metadata from known sources (e.g. PhishTank https://phishtank.org/ and PhishStats https://phishstats.info/). The data is used to feed the Brain component.

- PiSec Proxy: Proxy: an application that intercepts each HTTP connection request, and if the destination URL matches one of the URLs in the central repository, blocks it. The implementation is based on goproxy (https://github.com/elazarl/goproxy).
The membership test for the URL is carried out by a Bloom Filter, in order to maintain high performance with a small memory footprint. To further reduce the latency, the responses from the Brain are cached using Redis (https://redis.io/). 


