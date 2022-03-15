Title: TLS Fingerprinting
Date: 2022-02-17 20:30
Author: Nikolai Tschacher
Slug: TLS-Fingerprint
Status: published
Sortorder: 8

| <!-- -->         | <!-- -->                                      |
|------------------|-----------------------------------------------|
| **Author**       | Nikolai Tschacher                             |
| **API Version**  | v0.2                                          |
| **Version Date** | 18th February 2022                            |
| **API Access**   | Free                                          |
| **Download**     | Closed Source (Upon request only)             |

## API

The TLS fingerprinting API allows you to get your [TLS fingerprint](https://tls.incolumitas.com/fps). It can be used for various purposes such as:

1. Networking traffic analyisis 
2. Malware detection
3. Bot detection

### Endpoint `/fps`

The `/fps` endpoint returns the most recent TLS fingerprint of the client making the request. Internally, the client's public IP address is used to lookup matching TLS fingerprints. Since clients generate many TLS sessions over time, only the most recent TLS fingerprint is returned by default.

| Endpoint          | Description                                                                                                                                                                                                                                                             | Live API Call                                                                           |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| **/fps**          | This endpoint returns the most recent TLS fingerprint for the requesting client. | <a href="https://tls.incolumitas.com/fps">tls.incolumitas.com/fps</a>                   |
| **/fps?detail=1** | Request a detailed/verbose version of the TLS fingerprint for the current connection.                                                                                                                                                                                   | <a href="https://tls.incolumitas.com/fps?detail=1">tls.incolumitas.com/fps?detail=1</a> |
| **/fps?all=1**    | Request all TLS fingerprints for this client that exist in server memory (The server is restarted periodically).   All the fingerprints that match the client's IP address will be returned.                                                                            | <a href="https://tls.incolumitas.com/fps?all=1"> tls.incolumitas.com/fps?all=1 </a>     |

#### Example for endpoint `/fps`

Example by using `curl`:

```bash
curl 'https://tls.incolumitas.com/fps'
```

returns the following JSON response from the API:

```json
{
  "num_fingerprints": 13,
  "sha3_384": "47ff8777e0a84c9a3869c514b6de51943c2c063d55dd667344c5d1a3809df5f9bf9c700c7ea11debc29ad8fe9df8eeec",
  "tls_fp": {
    "ciphers": "52393,52392,52394,49200,49196,49192,49188,49172,49162,159,107,57,65413,196,136,157,61,53,192,132,49199,49195,49191,49187,49171,49161,158,103,51,190,69,156,60,47,186,65,49169,49159,5,4,49170,49160,22,10,255",
    "client_hello_version": "TLS 1.2",
    "ec_point_formats": "0",
    "extensions": "0,11,10,13,16",
    "record_version": "TLS 1.0",
    "signature_algorithms": "1537,1539,61423,1281,1283,1025,1027,61166,60909,769,771,513,515",
    "supported_groups": "29,23,24"
  },
  "user-agent": "curl/7.77.0",
  "utc_now": "2022-02-17 19:07:09.136016"
}
```


### Endpoint `/stats`

The `/stats` endpoint returns statistics over all stored TLS connections on the server side. Due to performance reasons, only the most recent 50MB of TLS data are considered in `/stats` lookups. Thus, the database is of reduced accuracy and statistical significance.

| Endpoint          | Description                                                                                                                                                                                                                                                             | Live API Call                                                                           |
|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| **/stats**        | Lists all TLS statistics.                                                                                                                                                                                                                                               | <a href="https://tls.incolumitas.com/stats">tls.incolumitas.com/stats</a>               |
| **/stats?bo=1**   | Get statistics only for TLS fingerprints with associated User-Agents                                                                                                                                                                                                    | <a href="https://tls.incolumitas.com/stats?bo=1">tls.incolumitas.com/stats?bo=1</a>     |


## Introduction

On this page, a server-side tool is presented, which extracts entropy from the TLS handshake in order to form a TLS fingerprint.

Such a TLS fingerprint may be used to identify devices / TLS protocol implementations. It is able to collect statistical data and correlate the TLS entropy with the `User-Agent` transmitted in HTTP headers. After this data collection process, questions such as:

1. Does the TLS fingerprint belong to the operating system that is claimed by the User Agent?
2. How unique is the TLS fingerprint among all clients?
3. Based on past observations and collected TLS client data, is this fingerprint a legit one?
4. To which TLS implementation does this fingerprint belong?

**Live TLS Entropy Detection:** This is your last seen TLS fingerprint - Taken from the initial TLS Client Hello handshake message:

<pre style="overflow: auto;" id="tls_fp">
...loading (JavaScript required)
</pre>

<script>
fetch('https://tls.incolumitas.com/fps')
  .then(response => response.json())
  .then(function(data) {
    document.getElementById('tls_fp').innerText = JSON.stringify(data, null, 2);
  })
</script>

Your User-Agent (`navigator.userAgent`) says that you are 

<pre style="overflow: auto;" id="userAgent">
</pre>

<script>
document.getElementById('userAgent').innerText = navigator.userAgent;
</script>


## TLS Fingerprint Definition

What fields from the TLS handshake is considered in the TLS fingerprint? Put differently: What sources of entropy does this tool use to build the TLS fingerprint?

Currently, the following data sources from the initial client `ClientHello` TLS handshake message are used:

+ **TLS Cipher Suites** - The preference-ordered list of [supported cipher suites](https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4) of the client. (Example: `"19018,4865,4866,4867,49195,49199,49196,49200,52393,52392,49171,49172,156,157,47,53"`)
+ **Client Hello Version** - The supported TLS version (Example: `"TLS 1.2"`)
+ **EC Point Formats** - The [EC point formats](https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-9) the client supports (Example: `"0,1,2"`)
+ **Extensions** - The list of [supported extensions](https://www.iana.org/assignments/tls-extensiontype-values/tls-extensiontype-values.xhtml#tls-extensiontype-values-1) (Example: `"56026,0,23,65281,10,11,35,16,5,13,18,51,45,43,27,17513,31354"`)
+ **Record Version** - The TLS record version, which is mostly `1.0` (Example: `"TLS 1.0"`)
+ **Signature Algorithms** - The list of supported client [signature algorithms](https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-16) (Example: `"1027,2052,1025,1283,2053,1281,2054,1537"`)
+ **Supported Groups** - The list of the [supported groups](https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-8) of the client (Example: `"56026,29,23,24"`)

Some TLS clients will randomize some TLS parameters for each new handshake. This is a small problem, but not a substantial one. For example, the following `ClientHello` differs slightly to the next `ClientHello` message:

```json
{
  "num_fingerprints": 10,
  "sha3_384": "a14851b3e6b9daa564f285c983ab929318875eeac94c56d02268bfb00ca37427e7d7d677140284f7aa4da36e0a8979de",
  "timestamp": 1643713939.2002213,
  "tls_fp": {
    "ciphers": "31354,4865,4866,4867,49195,49199,49196,49200,52393,52392,49171,49172,156,157,47,53",
    "client_hello_version": "TLS 1.2",
    "ec_point_formats": "0",
    "extensions": "14906,0,23,65281,10,11,35,16,5,13,18,51,45,43,27,17513,10794",
    "record_version": "TLS 1.0",
    "signature_algorithms": "1027,2052,1025,1283,2053,1281,2054,1537",
    "supported_groups": "47802,29,23,24"
  },
  "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
```

As it can be observed, the first element of `ciphers`, `extensions` and `supported_groups` seems to be chosen at random, which results in a different `sha3_384` fingerprint.

```json
{
  "num_fingerprints": 12,
  "sha3_384": "f18a2ee62ee0548fb09c5a31d4bbc61845cc53055c1640e381201d779a80a94e0d870fd48c2fc39fb5b15715ea731d95",
  "timestamp": 1643713950.4840238,
  "tls_fp": {
    "ciphers": "14906,4865,4866,4867,49195,49199,49196,49200,52393,52392,49171,49172,156,157,47,53",
    "client_hello_version": "TLS 1.2",
    "ec_point_formats": "0",
    "extensions": "64250,0,23,65281,10,11,35,16,5,13,18,51,45,43,27,17513,60138,21",
    "record_version": "TLS 1.0",
    "signature_algorithms": "1027,2052,1025,1283,2053,1281,2054,1537",
    "supported_groups": "35466,29,23,24"
  },
  "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
}
```

**Solution:** Only non-Reserved and non-Unassigned values for `ciphers`, `extensions` and `supported_groups` in the TLS fingerprint will be considered.


## Recommended Reading List

So you want to start fingerprinting TLS connections? It's plenty of fun. The following reading list is highly recommended:

1. [Slides - The Generation and Use of TLS Fingerprints](https://resources.sei.cmu.edu/asset_files/Presentation/2019_017_001_539902.pdf) - Cisco is doing advanced TLS fingerprinting and they [open sourced](https://github.com/cisco/joy) some of their TLS fingerprinting methodology and fingerprint database. They are also talking in a blog article named [TLS Fingerprinting in the Real World
](https://blogs.cisco.com/security/tls-fingerprinting-in-the-real-world) about the subject.
2. A rather new paper by researchers from the Technical University of Munich named [TLS Fingerprinting Techniques](https://www.net.in.tum.de/fileadmin/TUM/NET/NET-2020-04-1/NET-2020-04-1_04.pdf) is also a highly suggested read about TLS fingerprinting.
3. Another great read is a blog article named [TLS Fingerprinting with JA3 and JA3S from Salesforce](https://engineering.salesforce.com/tls-fingerprinting-with-ja3-and-ja3s-247362855967) which explains in-depth how Salesforce's JA3 and JA3S TLS fingerprinting works. The code for [JA3 and JA3S is open sourced](https://github.com/salesforce/ja3).


## TODO

- Add tool support for TLS 1.3
- Setup nginx to use TLS 1.3 on the server side
- Think about including the server response in the fingerprint as [JA3S](https://engineering.salesforce.com/tls-fingerprinting-with-ja3-and-ja3s-247362855967) does it: *After some time we found that, though servers will respond to different clients differently, they will always respond to the same client the same.*