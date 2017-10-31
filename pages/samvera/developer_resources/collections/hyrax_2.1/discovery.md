---
title: "FAQ - Collection Discovery"
keywords: Discovery, discoverable, discoverability, Collection, FAQ
categories: How to Do All the Things
permalink: collection_extensions_faq_discovery.html
folder: samvera/how-to/collection_extensions/hyrax_2.1/faq_discovery.md
sidebar: home_sidebar
tags: [development_resources]
a-z: ['FAQ - Collection Discovery', 'Collection Extensions - Discovery', 'Discovery of Collections']
version:
  label: 'Hyrax v2.1.0'
  branch:
    label: 'collections-sprint'
    link: 'https://github.com/samvera/hyrax/tree/collections-sprint'
---

### What does it mean for a collection to be discoverable?

Collections that are discoverable will have the collection itself included in search results, showing the collection title and summary metadata.

### Are items of a discoverable collection discoverable?

Not necessarily. The collection's setting for discovery does NOT effect the discoverability of the items in the collection.  All items in a collection are discoverable based on their own settings.

WHY NOT? The items in a collection can be in other collections which also have discoverability settings.  It would be complicated to sort through all the possible combinations and produce an intuitive, predicatable behavior.

### Do collections have to be public to be discoverable?

Yes, for public users.  For logged in users, collections will show up in search results if they can edit or read the collection.

### Where do I configure discoverability of a collection?

To edit discoverability of a collection:  Dashboard -> Collections -> click collection title -> Edit button -> Discovery tab

### Can I change the discoverability of a collection?

Yes.  Follow the instructions un 'Where do I configure discoverability of a collection'.

### If a collection type is NOT discoverable, can I set make collections of that type discoverable?

No, if the collection type of a collection has `discoverable=false`, the edit form will not show the Discovery tab.  All collections of this type will have `visibility=Private`.

### Why can't I set a collection to Public?

Whether a collection can be set to discoverable is determined by the collection type.  The site admin congifures a collection type to have DISCOVERY on/off.

### Where do I configure discoverability of a collection type?

To set DISCOVERY setting for a collection type: Dashboard -> Settings -> Collection Types -> Edit button beside collection type to modify -> Settings tab -> Check or uncheck DISCOVERY

### Can I change the discoverability setting of the collection type?

Yes and no... This setting is per collection type.  Once a collection of a type is created, the Settings configurations are not modifiable.  This includes includes the DISCOVERY setting.  Up to the point where you create a collection of that type, you can change the Settings, including DISCOVERY.
