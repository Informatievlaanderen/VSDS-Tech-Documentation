---
layout: default
title: 6. Fragmentations and pagination
nav_order: 6
has_children: true
has_toc: true
---

# LDES Fragmentations

To reduce the volume of data that consumers need to replicate or to speed up certain queries,
the LDES server can be configured to create several fragmentations.
Fragmentations are similar to indexes in databases but then published on the Web.
The RDF predicate on which the fragmentation must be applied is defined through configuration.

<p align="center"><img src="https://informatievlaanderen.github.io/VSDS-Tech-documentation/assets/images/fragmentation.png" width="60%" text-align="center"></p>

The fragmenting of a Linked Data Event Stream (LDES) is a crucial technique for managing and processing large amounts of data more efficiently.

An LDES focuses on allowing clients to replicate a dataset’s history and efficiently synchronise with its latest changes. Linked Data Event Streams may be fragmented when their size becomes too big for one HTTP response. Fragmenting an LDES has two main advantages:

It speeds up certain queries. E.g. an autocompletion client will solve its queries faster using a substring fragmentation than a lineair (append-only) fragmentation
It allows data consumers to replicate/stay in sync with only the part of the dataset they are actually interested in.
The most basic fragmentation of an LDES is called partitioning, which creates a linear fragmentation, appending the newest members on the latest fragment.

## Partitioning

By default, every Event Stream will be partitioned, wich means that the LDES server will create fragments based on the order of arrival of the LDES member.
The members arriving on the LDES server are added to the first page, while the latest members are always included on the latest page.

**Algorithm**

1. The fragment to which the member should be added is determined.
   - The currently open fragment is retrieved from the database.
   - If this fragment contains members equal to or exceeding the member limit or no fragment can be found, a new fragment is created instead.
2. If a new fragment is created, the following steps are taken.
   - The new fragment becomes the new open fragment and the previous fragment becomes immutable<sup>1</sup>.
   - This newly created fragment and the previous fragment are then linked with each other by 2 generic relationships<sup>1</sup>.
   - The pagenumber of the new fragment is determined based on the old fragment or is set to 1 in case of the first fragment.

<sup>1</sup> In case of the first fragment, a previous fragment does not exist so these steps are skipped.

## Supported Fragmentations:
