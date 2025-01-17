Title: IP Address API
Date: 2022-09-11 22:00
Modified: 2022-10-07 22:00
Author: Nikolai Tschacher
Slug: IP-API
Status: published
Sortorder: 5

| <!-- -->    | <!-- -->    |
|-------------|-------------|
| **Author**         | Nikolai Tschacher ([incolumitas.com](https://incolumitas.com/))     |
| **API Access**         | Free & unlimited (fair use)         |
| **API Version**         | **v0.9.6 (30th October 2022)**         |
| **API Endpoint**         | [https://api.incolumitas.com/?q=3.5.140.2](https://api.incolumitas.com/?q=3.5.140.2)         |
| **Total Tracked Hosting Providers**         |    [1566 hosting providers](https://incolumitas.com/pages/Hosting-Providers-List/)      |
| **Num Hosting Ipv4 Addresses**         |    **578,607** IPv4 CIDR ranges (1,124,037,736 Addresses in total)      |
| **Num Hosting Ipv6 Addresses**         |    **491,415** IPv6 CIDR ranges (1.3398119786246428e+33 Addresses in total)      |

---

The IP address API returns useful meta-information for IP addresses. For example, the API response includes the organization of the IP address, ASN information and geolocation intelligence.

Furthermore, the API response allows to derive security information for each IP address, for example whether an IP address belongs to a hosting provider (`is_datacenter`), is a TOR exit node (`is_tor`), if an IP address is a proxy (`is_proxy`) or belongs to an abuser (`is_abuser`).

This API strongly emphasises **datacenter/hosting detection**. A complicated hosting detection algorithm was developed to achieve a high detection rate. [Thousands of different hosting providers](https://incolumitas.com/pages/Hosting-Providers-List/) are tracked. Whois records, public hosting IP ranges from hosting providers and a proprietary hosting discovery algorithm are used to decide whether an IP address belongs to a datacenter or not.

The API includes accurate and rich ASN meta-data. For instance, the API output contains whois information for each active ASN and the ASN type is derived by analyzing the company that own the ASN.

## Live API

<div class="ipAPIDemo">
  <label style="font-weight: 600; font-size: 16px" for="ip">IP Address / ASN:</label>
  <input style="padding: 8px; margin-left: 4px; margin-right: 4px" type="text" id="ip" name="ip" value="13.34.52.117">
  <input class="orange_button" style="" type="submit" value="Make API Request" placeholder="IP Address or ASN">

  <div>
    <p><strong>Examples:</strong>
      <a class="api-example" data-query="165.227.176.0" href="#">165.227.176.0</a> —
      <a class="api-example" data-query="43.181.128.0" href="#">43.181.128.0</a> —
      <a class="api-example" data-query="143.81.28.0" href="#">143.81.28.0</a> —
      <a class="api-example" data-query="20.1.28.0" href="#">20.1.28.0</a> —
      <a class="api-example" data-query="194.126.177.0" href="#">194.126.177.0</a> —
      <a class="api-example" data-query="AS209103" href="#">AS209103</a> —
      <a class="api-example" data-query="23.236.48.55" href="#">23.236.48.55</a> —</br>
      <a class="api-example" data-query="2600:1F18:7FFF:F800:0000:ffff:0000:0000" href="#">2600:1F18:7FFF:F800:0000:ffff:0000:0000</a> —
      <a class="api-example" data-query="107.174.138.172" href="#">107.174.138.172</a> —
      <a class="api-example" data-query="107.152.214.169" href="#">107.152.214.169</a>
    </p>
  </div>

  <pre id="wrapper">
    <code id="data"class="JSON hljs">{
  "message": "Please make an API request",
}</code>
  </pre>
</div>

<script>
var el = document.getElementById('data');
hljs.highlightBlock(el);

function makeApiRequest(query) {
  let url = query ? 'https://api.incolumitas.com/?q=' + query : 'https://api.incolumitas.com/';

  fetch(url)
  .then(response => response.json())
  .then(function(data) {
    var el = document.getElementById('data');
    el.innerHTML = JSON.stringify(data, null, 2);
    hljs.highlightBlock(el);
    if (!query) {
      document.getElementById('ip').value = data.ip;
    }
  })
}

let linkNodes = document.querySelectorAll('a.api-example');

for (let node of linkNodes) {
  node.addEventListener('click', function(evt) {
    evt.preventDefault();
    let query = evt.target.getAttribute('data-query');
    document.getElementById('ip').value = query;
    makeApiRequest(query);
  });
}

makeApiRequest('');

document.querySelector('.ipAPIDemo input[type="submit"]').addEventListener('click', function(evt) {
  var ip = document.getElementById('ip').value;
  makeApiRequest(ip)
})
</script>

## Quickstart

Lookup any IP address: [https://api.incolumitas.com/?q=3.5.140.2](https://api.incolumitas.com/?q=3.5.140.2)

Lookup your own IP address: [https://api.incolumitas.com/](https://api.incolumitas.com/)

Usage with JavaScript:

```JavaScript
fetch('https://api.incolumitas.com/?q=23.236.48.55')
  .then(res => res.json())
  .then(res => console.log(res));
```

Usage with Curl:

```bash
curl 'https://api.incolumitas.com/?q=32.5.140.2'
```

## Introduction

The IP adddress API internally uses the following data sources:

1. Public whois records from regional Internet address registries such as RIPE NCC, APNIC, ARIN and so on
2. BGP information in order to find ASN information and their associated routes/prefixes
3. Public blocklists such as [firehol/blocklist-ipset](https://github.com/firehol/blocklist-ipsets)
4. The API uses several proprietary datacenter/hosting detection algorithms
5. Other open source projects that try to find hosting IP addresses such as [github.com/client9/ipcat](https://github.com/client9/ipcat), [github.com/Umkus/ip-index](https://github.com/Umkus/ip-index) or [https://github.com/X4BNet/lists_vpn](github.com/X4BNet/lists_vpn) are also considered.
6. The API uses IP threat data from various honeypots
7. IP geolocation information from several different geolocation providers is used

### API Features

+ **Ready for production**: This API can be used in production and is stable
+ **Many datacenters supported:** [Thousands of different hosting providers and counting](https://incolumitas.com/pages/Hosting-Providers-List/) - From Huawei Cloud Service to ServerMania Inc. Find out whether the IP address is hosted by looking at the `is_datacenter` property!
+ **Always updated**: The API database is automatically updated several times per week.
+ **AS (Autonomous System) support**: The API provides autonomous system information for each looked-up IP address
+ **Company Support**: The API provides organisational information for each network of each looked up IP address
+ **Bulk IP Lookups**: You can lookup up to 100 IP addresses per API call

## API Response Format

The API output format is explaind by walking through an example. Most of the returned information is self-explanatory.

This is how a typical API response looks like. The IP `107.174.138.172` was queried with the API call [https://api.incolumitas.com/?q=107.174.138.172](https://api.incolumitas.com/?q=107.174.138.172)):

```json
{
  "ip": "107.174.138.172",
  "rir": "ARIN",
  "is_bogon": false,
  "is_datacenter": true,
  "is_tor": true,
  "is_proxy": false,
  "is_abuser": true,
  "company": {
    "name": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.172.0.0 - 107.175.255.255",
    "whois": "https://api.incolumitas.com/?whois=107.172.0.0"
  },
  "datacenter": {
    "datacenter": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.174.138.0/24"
  },
  "asn": {
    "asn": 36352,
    "route": "107.174.138.0/24",
    "descr": "AS-COLOCROSSING, US",
    "country": "us",
    "active": true,
    "org": "ColoCrossing",
    "domain": "colocrossing.com",
    "abuse": "abuse@colocrossing.com",
    "type": "hosting",
    "created": "2005-12-12",
    "updated": "2013-01-08",
    "rir": "arin",
    "whois": "https://api.incolumitas.com/?whois=AS36352"
  },
  "location": {
    "country": "us",
    "state": "New York",
    "city": "Buffalo",
    "latitude": "42.882500",
    "longitude": "-78.878800",
    "zip": "14202",
    "timezone": "-04:00",
    "local_time": "2022-10-30T15:21:04.783Z"
  },
  "elapsed_ms": 0.84
}
```

In the following section, the different parts of the API response are explained in-depth.

### Response Format: Top Level API Output

The top level API output looks as follows:

```json
{
  "ip": "107.174.138.172",
  "rir": "ARIN",
  "is_bogon": false,
  "is_datacenter": true,
  "is_tor": true,
  "is_proxy": false,
  "is_abuser": true,
  "elapsed_ms": 0.62
}
```

The explanation for those fields is as follows:

+ `ip` - `string` - the IP address that was looked up, here it was `107.174.138.172`
+ `rir` - `string` - to which [Regional Internet Registry](https://en.wikipedia.org/wiki/Regional_Internet_registry) the IP address belongs. Here it belongs to `ARIN`, which is the RIR responsible for North America
+ `is_bogon` - `boolean` - Whether the IP address is bogon. For example, the loopback IP `127.0.0.1` is a special/bogon IP address. The IP address `107.174.138.172` is not bogon, hence it is set to `false` here.
+ `is_datacenter` - `boolean` - whether the IP address belongs to a datacenter. Here, we have the value `true`, since `107.174.138.172` belongs to the datacenter provider ColoCrossing.
+ `is_tor` - `boolean` - is true if the IP address belongs to the TOR network. This is the case here.
+ `is_proxy` - `boolean` - whether the IP address is a proxy. This is not the case here.
+ `is_abuser` - `boolean` - is true if the IP address committed abusive actions, which was the case with `107.174.138.172`
+ `elapsed_ms` - `float` - how much internal processing time was spent in ms. This lookup only took `0.62ms`, which is quite fast.

### Response Format: The `datacenter` object

```json
  "datacenter": {
    "datacenter": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.174.138.0/24"
  },
```

If the IP address belongs to a datacenter/hosting provider, the API response will include a `datacenter` object with the following attributes:

+ `datacenter` - `string` - to which datacenter the IP address belongs. For a full list of datacenters, check the [api.incolumitas.com/info endpoint](https://api.incolumitas.com/info). In this case, the datacenter's name is `ColoCrossing`.
+ `domain` - `string` - The domain name of the company
+ `network` - `string` - the network this IP address belongs to (In the above case: `107.174.138.0/24`)

Most IP's don't belong to a hosting provider. In those cases, the `datacenter` object will not be present.

For a couple of large cloud providers, such as Google Cloud, Amazon AWS, DigitalOcean or Microsoft Azure (and some others), the `datacenter` object is more detailed.

Amazon AWS example:

```json
{
  "ip": "3.5.140.2",
  "datacenter": {
    "cidr": "3.5.140.0/22",
    "region": "ap-northeast-2",
    "datacenter": "Amazon AWS",
    "service": "EC2",
    "network_border_group": "ap-northeast-2"
  },
}
```

DigitalOcean example:

```json
{
  "ip": "167.99.241.135",
  "datacenter": {
    "cidr": "167.99.240.0/20",
    "datacenter": "DigitalOcean",
    "code": "60341",
    "city": "Frankfurt",
    "state": "DE-HE",
    "country": "DE"
  },
}
```

### Response Format: The `company` object

```json
  "company": {
    "name": "ColoCrossing",
    "domain": "colocrossing.com",
    "network": "107.172.0.0 - 107.175.255.255",
    "whois": "https://api.incolumitas.com/?whois=107.172.0.0"
  },
```

Most IP addresses can be associated with an organization or company. The API uses whois database information to infer which organization is the owner of a certain IP address. Most API lookups will have an `company` object with the following attributes:

+ `name` - `string` - The name of the company
+ `domain` - `string` - The domain name of the company
+ `network` - `string` - The network for which the company has ownership
+ `whois` - `string` - An url to the whois information for the network of this IP address

### Response Format: The `asn` object

```json
  "asn": {
    "asn": 36352,
    "route": "107.174.138.0/24",
    "descr": "AS-COLOCROSSING, US",
    "country": "us",
    "active": true,
    "org": "ColoCrossing",
    "domain": "colocrossing.com",
    "abuse": "abuse@colocrossing.com",
    "type": "hosting",
    "created": "2005-12-12",
    "updated": "2013-01-08",
    "rir": "arin",
    "whois": "https://api.incolumitas.com/?whois=AS36352"
  },
```

Most IP addresses can be associated with an Autonomeous System (AS). The `asn` object provides the following attributes:

+ `asn` - `int` - The AS number
+ `route` - `string` - The IP route as CIDR in this AS
+ `descr` - `string` - An informational description of the AS
+ `country` - `string` - The country where the AS is situated in (administratively)
+ `active` - `string` - Whether the AS is active (active = at least one route administred by the AS)
+ `domain` - `string` - The domain of the organization to which this AS belongs
+ `org` - `string` - The organization responisible for this AS
+ `type` - `string` - The type for this ASN, this is either `hosting`, `education`, `goverment`, `banking`, `business` or `isp`
+ `created` - `string` - When the ASN was established
+ `updated` - `string` - The last time the ASN was updated
+ `rir` - `string` - To which Regional Internet Registry the ASN belongs
+ `whois` - `string` - An url to the whois information for this ASN

For inactive autonomeous systems, most of the above information is not available.

### Response Format: The `location` object

```json
  "location": {
    "country": "us",
    "state": "New York",
    "city": "Buffalo",
    "latitude": "42.882500",
    "longitude": "-78.878800",
    "zip": "14202",
    "timezone": "-04:00",
    "local_time": "2022-10-30T15:22:31.183Z"
  },
```

The API provides geolocation information for the looked up IP address. The `location` object includes the following attributes:

+ `country` - `string` - The ISO 3166-1 alpha-2 country code to which the IP address belongs. This is the country specific geolocation of the IP address.
+ `state` - `string` - The state for the IP address
+ `city` - `string` - The city to which the IP address belongs
+ `latitude` - `string` - The latitude for the IP address
+ `longitude` - `string` - The longitude for the IP address
+ `zip` - `string` - The zip code for this IP
+ `timezone` - `string` - The timezone for this IP
+ `local_time` - `string` - The local time for this IP
+ `possible_other_location` - `array` - (Optional) -  If there are multiple possible locations, the attribute `possible_other_location` is included in the API response. It contains an array of ISO 3166-1 alpha-2 country codes which represent the possible other geolocation countries.

Country level geolocation accuracy is quite good, since the API provides information from several different geolocation service providers.

## API Endpoints

The IP API currently has two endpoints.

### GET - `/` - Lookup a single IP address or ASN

This GET endpoint allows to lookup a single IPv4 or IPv6 IP address by specifying the query parameter `q`. Example: `q=142.250.186.110`. You can also lookup **ASN** numbers by specifying the query `q=AS209103`.

| <!-- -->         | <!-- -->                                           |
|------------------|----------------------------------------------------|
| **Endpoint**       | /                                  |
| **Method**       | `GET`                                  |
| **Parameter**       | `q` - The IP address or ASN to lookup                                 |
| **Example** | [https://api.incolumitas.com/?q=3.5.140.2](https://api.incolumitas.com/?q=3.5.140.2)
| **ASN Example** | [https://api.incolumitas.com/?q=AS42831](https://api.incolumitas.com/?q=AS42831)

### POST - `/` - Lookup up to 100 IP addresses in one API call

You can also make a bulk API lookup with up to 100 IP addresses (Either IPv4 or IPv6) in one single request.

| <!-- -->         | <!-- -->                                           |
|------------------|----------------------------------------------------|
| **Endpoint**       | /                                  |
| **Method**       | `POST`                                  |
| **Content-Type**       | `Content-Type: application/json`                                  |
| **Parameter**       | `ips` - An array of IPv4 and IPv6 addresses to lookup                                 |

For example, in order to lookup the IP addresses

+ `162.158.0.0`
+ `2406:dafe:e0ff:ffff:ffff:ffff:dead:beef`
+ `162.88.0.0`
+ `20.41.193.225`

you can use the following POST API request with curl:

```bash
curl --header "Content-Type: application/json" \
  --request POST \
  --data '{"ips": ["162.158.0.0", "2406:dafe:e0ff:ffff:ffff:ffff:dead:beef", "162.88.0.0", "20.41.193.225"]}' \
  https://api.incolumitas.com/
```

## Change Log

#### 30th October 2022

+ Better geolocation information
+ Updated whole database
+ Updated this API documentation page

#### 30th September 2022

+ Moved the API to a dedicated server. There will be much fewer service disruptions from now on
+ Added more detailed geolocation information
+ Updated whole database
+ Added LACNIC organization information
+ Added field `type` to `asn` objects. Possible `asn` `type` values: `hosting`, `education`, `goverment`, `banking`, `business`, `isp`
+ Updated this API documentation page
+ Info: The API has passed 10 Million requests

#### 11th September 2022

+ Updated this API documentation page to reflect that the API is no longer only a datacenter IP address API, but a more generic and powerful IP API

#### 21th August 2022

+ Added country specific geolocation information
+ Updated datacenter ranges and added new datacenters
+ Now providing company information for almost all active networks in the Internet
+ Providing organisation and abuse meta information for each of the 79247 active ASN's

#### 30th July 2022

+ Added hundreds of new datacenters (Now 496 datacenters in the database)
+ Added basic country geolocation for each IP (In the attribute `location`)
+ Server is now faster and has more RAM (no more downtime)
+ Updated this documentation page

#### 26th July 2022

+ Updated this documentation page
+ The API provides now AS (Autonomous System) information for each looked up IP address (in the attribute `asn`)
+ The API provides information about the Regional Internet Registrity to which the looked up IP belongs (in the attribute `rir`)
+ Added a huge amount of new hosting providers / datacenters to the API

#### 5th June 2022

+ Added [APNIC whois data](https://www.apnic.net/) to database
+ Updated database in general
+ added API endpoint [/info](https://api.incolumitas.com/info)

#### 20th March 2022

+ Improved API
+ Remove the `service` attribute in API output. Only attribute that identifies the cloud provider is now `datacenter`
+ Add bulk IP lookup mode. Allow up to 100 ips in bulk lookup mode. Only return datacenter IP addresses. Uses POST method. Ignore Invalid IPs

#### 14th March 2022 and 15th March 2022

+ Added **39,246** IPv4 and **360,372** IPv6 CIDR ranges ranges to the database from AFRINIC, RIPE-NCC, APNIC, ARIN and LACNIC whois databases.
+ Some Examples: [Lookup of Cloudflare IP](https://api.incolumitas.com/datacenter?ip=104.28.48.132), [Lookup of M247 Ltd IP](https://api.incolumitas.com/datacenter?ip=193.27.14.71)
, [Lookup of Packethub S.A. IP](https://api.incolumitas.com/datacenter?ip=185.153.176.242),  [Lookup of Leaseweb IP](https://api.incolumitas.com/datacenter?ip=178.162.192.22)
+ Added the following datacenters to the API: `XT Global Networks LTD`, `OVH`, `myLoc`,
`ServiHosting Networks S.L.`, `Clouvider Limited`,
`Hetzner Online`, `GoDaddy Operating Company, LLC.`,
`Claranet limited`, `Selectel Ltd.`, `M247 Ltd`,
`Imperva, Inc.`, `Beget`, `trueserver.nl`, `1&1 Internet`,
`DediPath`, `iomart Hosting Ltd`,
`Aruba S.p.a`, `Strato AG`, `DataCamp Limited`,
`Rackspace, Inc.`, `Reg.Ru Hosting`, `Heficed`, `LeaseWeb`,
`Mittwald`, `The Constant Company, LLC`,
`American Internet Services`, `Hostinger`,
`PlusServer GmbH`, `Equinix, Inc.`, `GHOSTnet GmbH`,
`It7 Networks Inc`,
`xservers.ro`, `Cloudflare`, `IBM Cloud`,
`iWeb Technologies Inc.`,
`SysEleven SysEleven GmbH`, `G-Core Labs S.A.`,
`Tencent Cloud`, `Choopa, LLC.`, `DigitalOcean`,
`Webair Internet Development Inc`, `Hostway`,
`Phoenix NAP, LLC`, `Contabo GmbH`, `A2 Hosting`,
`Zenlayer`, `Optimate Server`, `LogicWeb Inc.`,
`Packethub S.A.`, `MULTACOM Inc.`, `veesp.com vps clients`,
`Sakura Internet Inc.`, `Zscaler, Inc.`,
`Vultr Holdings LLC`, `Enzu Inc.`, `24Shells Inc`,
`GZ Systems (PureVPN)`, `HIVELOCITY, Inc.`, `Transip Bv.`,
`Host Europe`, `Internap Corporation`, `IP Exchange GmbH`,
`Datapipe`, `Savvis`,
`home.pl S.A.`, `Redstation Limited`, `hosting.ua`,
`Daou Technology`, `Linode`, `Amazon AWS`,
`kinx.net`, `Dreamscape`, `Xneelo (Pty) Ltd`,
`Fiber Grid Inc`, `Performive LLC`, `Microsoft Azure`,
`UCloud`, `Hostgator`, `Hostwinds.com`, `Namecheap`,
`Stackpath, LLC`, `ALL INKL`,
`Online SAS (Scaleway)`, `ServerCentral`,
`Quadranet, Inc`, `FDC Servers`,
`ColoCrossing`, `Aptum Technologies`, `Sharktech Inc.`,
`Wholesale Internet, Inc`, `Dreamhost`,
`Pair Networks`, `ServerHub`, `Psychz Networks`,
`CoreSpace,Inc.`, `Cologix, Inc.`,
`Colocation America Inc`, `ServerMania Inc.`, `Peak10`,
`Google Cloud`, `Fasthosts Internet Ltd`,
`latisys`, `Mullvad VPN`, `InMotion`, `Unified Layer`,
`Fastly, Inc.`, `Ubiquity Hosting`.

#### 11th March 2022

+ Added bulk IP lookup mode, up to 100 IP addresses can be queried with one API request
+ Added fast IPv6 lookup support (Speedup of factor `883x`, average lookup time now  `0.056ms` instead of `49.99ms`)

#### 9th March 2022

+ Added `cidr` attribute to the API output
+ Improved API performance for IPv4 (IPv6 still pending), the performance is now 1500x times faster on average (Average lookup time before: `100.37ms`, average lookup time now `0.068ms`)

#### 8th March 2022

+ Added cloud service providers / hosting providers / VPN service providers: `Linode`, `Mullvad VPN`, `B2 Net Solutions Inc.`, `scaleway.com`, `Vultr Holdings, LLC`, `The Constant Company, LLC`, `QuadraNet Enterprises LLC`, `Zscaler, Inc.`, `CloudCone, LLC`, `Psychz Networks`,  `BuyVM Services (buyvm.net)`,  `DataCamp Limited`,  `ColoCrossing`,  `IT7 Networks Inc.`,  `G-Core Labs S.A.`,  `AlmaHost Ltd`,  `Reg.Ru Hosting`,  `Packethub S.A.`,  `Clouvider Limited`,  `24Shells Inc`,  `Performive LLC`,  `Packet Host, Inc.`,  `veesp.com vps clients`,  `tzulo, inc.`,  `Cluster Logic Inc`,  `Owl Limited`,  `HIVELOCITY, Inc.`, `SysEleven SysEleven GmbH`
+ Added lookup time in `ms` to the API output as attribute `elapsed_ms`
+ Updated this API page
+ Updated all IP address ranges

#### 6th November 2021

+ Updated all IP address ranges
+ Add cloud provider `m247 Ltd`, `servers.com Inc.`, `Leaseweb Usa Inc.`, often used for proxies/fraud
+ Checking for common datacenter in `whois` lookup

#### 29th September 2021

+ Updated all IP address ranges
+ Added dedicated API endpoint: <https://api.incolumitas.com/datacenter?ip=3.5.140.2>
+ Old API Endpoint: <https://abs.incolumitas.com/datacenter?ip=1.2.3.4>
