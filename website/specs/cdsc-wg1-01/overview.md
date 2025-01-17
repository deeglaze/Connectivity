---
layout: base
nav: specs
title: CDSC-WG1 - 01 Server Metadata - Overview
meta_description: Linux Foundation Energy - Carbon Data Specification Consortium (CDSC) - Connectivity Working Group (WG1) - Specifications - cdsc-wg1-01 - Server Metadata - Overview
---
[Home]({{ "/" | relative_url }}) / [Specifications]({{ "/specs" | relative_url }}) / [`cdsc-wg1-01` - Server Metadata]({{ "/specs/cdsc-wg1-01" | relative_url }}) / Overview

# Server Metadata - Overview

This is a summary overview of [`cdsc-wg1-01`]({{ "/specs/cdsc-wg1-01" | relative_url }}), which
describes how utility and other central entity servers ("Servers") provide a structured reference of
information about them, the functionality they offer, and how other entities ("Clients") can
interoperate with them.

**NOTE:** Utility and other central entity servers ("Servers") are not always run by utilities themselves. They
could be other types of energy companies, such as electric retailers or community choice aggregators
(CCAs). They can also be centralized entities such as state-run data warehouses or private
infrastructure providers. Throughout this overview, the term "utility or entity" is used instead of
just "utility" to describe the organizations providing access to utility data and functionality.

## Background <a id="background" href="#background" class="permalink">🔗</a>

There are thousands of utilities serving customers across the world, and each has their own way of
organizing and structuring data and functionality. For [use case]({{ "/use-cases" | relative_url }})
companies needing to access data or functionality, an efficient means of discovering, registering, and
interoperating with these utilities is needed.

## Metadata Endpoint <a id="metadata-endpoint" href="#metadata-endpoint" class="permalink">🔗</a>

Server metedata is provided under the [well-known url](https://datatracker.ietf.org/doc/html/rfc8615)
`"/.well-known/carbon-data-spec.json`. The response is a [structured object](#metadata-structure)
containing information about the server, what functionality it offers, and how to interoperate with
it.

#### Metadata Example <a id="metadata-example" href="#metadata-example" class="permalink">🔗</a>
```
==Request==
GET /.well-known/carbon-data-spec.json HTTP/1.1
Host: demoutility.com

==Response==
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
    "cds_metadata_version": "v1",
    "cds_metadata_url": "https://demoutility.com/.well-known/carbon-data-spec.json",

    "created": "2022-01-01T00:00:00.000000Z",
    "updated": "2022-06-01T00:00:00.000000Z",

    "name": "Demo Gas & Electric",
    "description": "A fictional utility that can be used for testing and demonstration purposes.",
    "website": "https://demoutility.com/data-access",
    "documentation": "https://demoutility.com/data-access/docs",
    "support": "https://demoutility.com/contact-us?type=developers",
    "infrastructure_types": [
        "distribution_utility",
        "metering_provider,
        "supplier"
    ],
    "commodity_types": [
        "electricity",
        "natural_gas"
    ],
    "capabilities": [
        "coverage",
        "oauth",
        "ieee1547"
    ],
    "related_metadata": ["https://static.demoutility.com/sister-company-cds.json"],

    "coverage": "https://static.demoutility.com/cds-coverage.json",

    "oauth_metadata": "https://demoutility.com/.well-known/oauth-authorization-server",

    "ieee1547_setup": "https://demoutility.com/smart-inverters/setup"
}
```

#### Metadata Structure <a id="metadata-structure" href="#metadata-structure" class="permalink">🔗</a>

Overall information about the Server is included at the object root level:

* `cds_metadata_version` - _string_ - (required) Which version of server metadata structure this server follows (currently `v1` is the only version)
* `cds_metadata_url` - _string_ - (required) A URL link to this metadata object's canonical network location
* `created` - _ISO8601 datetime_ - (required) When the metadata url was first made available
* `updated` - _ISO8601 datetime_ - (required) When this metadata object was last modified.
* `name` - _string_ - (required) The name of the utility or entity (e.g. "Demo Gas & Electric")
* `description` - _string_ - (required) A brief description about the utility or entity (e.g. "An electric and gas utility serving customers in Northern California")
* `website` - _URL_ - (required) Where users can learn more about the utility or entity and its data access capabilities
* `documentation` - _URL_ - (required) Where users can find the utility or entity's technical reference documentation
* `support` - _URL_ - (required) Where Clients can find contact information for technical support on the Server's capabilities
* `infrastructure_types` - _Array[[InfrastructureType](#infrastructure-types)]_ - (required) A list of Infrastructure Types that are applicable to this utility or entity
* `commodity_types` - _Array[[CommodityType](#commodity-types)]_ - (required) A list of Commodity Types that are applicable to this utility or entity
* `capabilities` - _Array[[Capability](#capabilities)]_ - (required) What Carbon Data Specification functionality the utility or entity supports
* `coverage` - _URL_ - (optional) Where to find the structured [Coverage](#coverage-endpoint) details for the utility or entity
* `related_metadata` - _Array[URL]_ - (optional) Where to find other related [Metadata Endpoints](#metadata-endpoint)

Other components are listed in the `capabilities`, where each listed component can optionally add additional metadata fields. Except for `coverage`, listed capabilities are defined in their own extensions and specifications.

#### Infrastructure Types <a id="infrastructure-types" href="#infrastructure-types" class="permalink">🔗</a>

* `distribution_utility` - A electric, natural gas, and/or water utility that distributes the utility service to the end customer (e.g. "who maintains the wires to your house")
* `metering_provider` - An entity that maintains the utility metering infrastructure and/or reads end customer meters (e.g. "who reads your meter")
* `supplier` - An energy supplier for the end customer (e.g. "who you buy power from")
* `central_repository` - A government mandated entity that collects customer usage information into a centralized repository for re-distribution (e.g. a state-level meter data warehouse for deregulated jurisdictions)

#### Commodity Types <a id="commodity-types" href="#commodity-types" class="permalink">🔗</a>

* `electricity` - Has data and/or functionality around utility electric services
* `natural_gas` - Has data and/or functionality around utility natural gas services
* `fuel_oil` - Has data and/or functionality around utility fuel oil services
* `water` - Has data and/or functionality around utility water services

#### Capabilities <a id="capabilities" href="#capabilities" class="permalink">🔗</a>

Below is the list of supported capabilities in this specification:

* `coverage` - The Server is providing coverage details

And here are some other extensions that add capabilities for Servers:

* `oauth` - See [`cdsc-wg1-02`]({{ "/specs/cdsc-wg1-02" | relative_url }}) - How Clients can register with the Server in order to access data or functionality that requires

Servers can extend their list of capabilities with other non-CDS functionalities. For example, if the utility or entity provides connectivity for smart inverters (e.g. IEEE 1547) and wants to list that as a capability in its metadata, it can include something like `ieee1547` as a capability, so that Clients who know how to interpret that capability can auto-discover it.

Clients MUST ignore any capabilities they do not recognize.

## Coverage Endpoint <a id="coverage-endpoint" href="#coverage-endpoint" class="permalink">🔗</a>

This endpoint provides a structured metadata object on what territory, services, and customer
segments for which the utility or entity provides support (i.e. "what it covers").

The returned resource follows the [Coverage Structure](#coverage-structure).

#### Coverage Example <a id="coverage-example" href="#coverage-example" class="permalink">🔗</a>
```
==Request==
GET /cds-coverage.json HTTP/1.1
Host: static.demoutility.com

==Response==
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8

{
    "coverage_entries": [
        {
            "id": "norcal",
            "type": "electric_service_territory",
            "updated": "2022-06-01T00:00:00.000000Z",
            "name": "DG&E Electric Service Territory",
            "description": "Covers most of northern California from Fresno to Redding",
            "map_resource": "https://static.demoutility.com/cds-coverage-map.png",
            "map_content_type": "image/png",
            "geojson_resource": "https://static.demoutility.com/cds-coverage-map-geo.json"
        }
    ],
    "next": null,
    "previous": null
}
```

#### Coverage Structure <a id="coverage-structure" href="#coverage-structure" class="permalink">🔗</a>

The coverage object has the following fields at the root level:

* `coverage_entries` - _Array[[CoverageEntry](#coverage-entry)]_ - (required) A list of segments this utility or enitity covers
* `next` - _`null` or URL_ - (required) Link to the next page of coverage entries (`null` if no more subsequent pages)
* `previous` - _`null` or URL_ - (required) Link to the previous page of coverage entries (`null` if no more prior pages)

#### Coverage Entry <a id="coverage-entry" href="#coverage-entry" class="permalink">🔗</a>

The coverage area object has the following fields at the root level:

* `id` - _string_ - (required) A unique identifier for the coverage entry
* `type` - _[CoverageEntryType](#coverage-entry-types)_ - (required) Which Coverage Entry Type this coverage entry is
* `updated` - _ISO8601 datetime_ - (required) When this coverage entry was last modified.
* `name` - _string_ - (required) A human readable name for the coverage area or category (e.g. "Upstate New York")
* `description` - _string_ - (optional) A brief description the coverage area or category (e.g. "Most of the northern half of New York state")
* `map_resource` - _URL_ - (optional) Link to a map of the coverage territory
* `map_content_type` - _MIME type_ - (optional) Content-Type format of the `map_resource`
* `geojson_resource` - _URL_ - (optional) Link to a [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946) object mapping the coverage territory

#### Coverage Entry Types <a id="coverage-entry-types" href="#coverage-entry-types" class="permalink">🔗</a>

* `electric_service_territory` - Geographic area where data and/or functionality for utility electric services is supported
* `natural_gas_service_territory` - Geographic area where data and/or functionality for utility natural gas services is supported
* `fuel_oil_service_territory` - Geographic area where data and/or functionality for utility fuel oil services is supported
* `water_service_territory` - Geographic area where data and/or functionality for utility water services is supported

---

# Other Drafts <a id="other-drafts" href="#other-drafts" class="permalink">🔗</a>

* Maintainer's draft: [[Website](https://daniel-utilityapi.github.io/Connectivity/specs/cdsc-wg1-01/overview)] [[Code](https://github.com/daniel-utilityapi/Connectivity/blob/main/website/specs/cdsc-wg1-01/overview.md)]

