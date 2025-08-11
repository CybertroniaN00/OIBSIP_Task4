
# HTTP PACKET ANALYSIS REPORT

## INTRODUCTION

Introduction
For this task, I used Wireshark, a powerful network protocol analyzer, to capture and analyze HTTP traffic from my system. The goal was to inspect real network communication, identify request–response patterns, and understand the details of HTTP packets exchanged between my device and remote servers.

I began by launching Wireshark and selecting the active network interface connected to the internet. Once the capture started, I applied the filter http to isolate only HTTP traffic, excluding other unrelated packets.

## OVERVIEW

This document provides a packet-by-packet analysis  HTTP packets captured in Wireshark.
These packets show a combination of:
- Windows automatic network connectivity checks
- Browser asset requests 
- Responses from Microsoft and Google servers
- All captured over HTTP 



## PACKET ANALYSIS

### Packet 1

Purpose:
Windows initiates a connectivity check by requesting a plain text file from Microsoft's NCSI server.

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 49.44.178.168
- Source Port: Random high port (e.g., 49876)
- Destination Port: 80 (HTTP)
- Protocol: HTTP
- Request Line: GET /connecttest.txt HTTP/1.1
- Host Header: www.msftconnecttest.com
- Headers:
  - Cache-Control: no-cache
  - Pragma: no-cache
  - User-Agent: Microsoft NCSI

Context:
Windows expects the file to contain the string "Microsoft Connect Test" to confirm internet access.

Security Observations:
- OS fingerprinting possible (NCSI user-agent reveals Windows use)
- No encryption; vulnerable to MITM alteration.


### Packet 2

Purpose:
Microsoft server returns the requested connectivity check file.

Technical Details:
- Source IP: 49.44.178.168
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 200 OK
- Headers:
  - Date: Mon, 11 Aug 2025
  - Content-Length: 22
  - Content-Type: text/plain
  - Cache-Control: max-age=30, must-revalidate
- Body: Microsoft Connect Test

Context:
Confirms network connectivity to Windows OS.

Security Observations:
- Could be spoofed to make a captive portal appear as open internet.


### Packet 3

Purpose:
Browser requests favicon.ico from Microsoft test domain.

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 49.44.178.168
- Protocol: HTTP
- Request Line: GET /favicon.ico HTTP/1.1
- Host Header: www.msftconnecttest.com

Context:
Browsers attempt favicon retrieval automatically for UI display.

Security Observations:
- Reveals domain being accessed.
- HTTP request exposes browsing patterns.


### Packet 4

Purpose:
Microsoft server indicates favicon.ico file does not exist.

Technical Details:
- Source IP: 49.44.178.168
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 404 Not Found
- No body content.

Context:
Normal HTTP error for missing resource.

Security Observations:
- No sensitive information disclosed.


### Packet 5

Purpose:
Client requests a web resource from Google’s servers.

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 172.217.166.196
- Protocol: HTTP
- Request Line: GET /<resource> HTTP/1.1
- Host Header: google.com or service under it.

Context:
Could be triggered by user navigation or background process.

Security Observations:
- HTTP request path is visible.
- Susceptible to interception.


### Packet 6

Purpose:
Google server delivers requested content.

Technical Details:
- Source IP: 172.217.166.196
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 200 OK
- Headers: Content-Type, Content-Length, Cache details.
- Body: Likely HTML or JSON data.

Context:
Page content or API data delivered to client.

Security Observations:
- Data is visible in plaintext.


### Packet 7

Purpose:
Client requests an additional resource (CSS, JS, or image).

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 172.217.166.196
- Protocol: HTTP
- Request Line: GET /<asset> HTTP/1.1

Context:
Secondary assets required for full page rendering.

Security Observations:
- HTTP reveals exact filenames and structure.


### Packet 8

Purpose:
Google server returns the requested additional resource.

Technical Details:
- Source IP: 172.217.166.196
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 200 OK
- Body: Asset file content.

Security Observations:
- Asset data visible and modifiable in transit.


### Packet 9

Purpose:
Client requests yet another linked resource.

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 172.217.166.196
- Protocol: HTTP
- Request Line: GET /<resource> HTTP/1.1

Context:
Part of continued page load.

Security Observations:
- HTTP continues to leak browsing behavior.


### Packet 10

Purpose:
Google delivers the additional requested resource.

Technical Details:
- Source IP: 172.217.166.196
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 200 OK
- Body: Content data.

Security Observations:
- No encryption; could be replaced or read in transit.


### Packet 11

Purpose:
Client requests a final needed file for page completion.

Technical Details:
- Source IP: 192.168.137.214
- Destination IP: 172.217.166.196
- Protocol: HTTP
- Request Line: GET /<final_resource> HTTP/1.1

Context:
Completes the asset fetching process for the webpage.

Security Observations:
- HTTP request exposes final asset type and location.


### Packet 12

Purpose:
Google server responds with the final requested file.

Technical Details:
- Source IP: 172.217.166.196
- Destination IP: 192.168.137.214
- Protocol: HTTP
- Status Line: HTTP/1.1 200 OK
- Body: Final asset content.

Security Observations:
- Still plaintext; lacks confidentiality and integrity protection.


## KEY FINDINGS

1. All packets used HTTP, exposing full request/response data.
2. Automated OS requests (Packets 1–4) reveal device type and behavior.
3. Browsing patterns and asset names are visible in captured traffic.
4. MITM attacks possible due to lack of encryption.


