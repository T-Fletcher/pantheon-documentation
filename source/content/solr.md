---
title: Apache Solr on Pantheon
description: Detailed information on using Apache Solr on your Pantheon Drupal or WordPress site.
tags: [addons]
categories: []
---
Apache Solr is a system for indexing and searching site content. Pantheon provides Apache Solr as a service for most plans including Sandbox, on all environments. No permission or action is required from Pantheon to use Solr. 
`markdown:/solr-version.md`


<Enablement title="Get DevOps Training" link="https://pantheon.io/agencies/learn-pantheon?docs">

Learn how to configure Solr with help from our experts. Pantheon delivers custom workshops to help development teams master the platform and improve internal DevOps.

</Enablement>

## Which Plans Can Use Solr?

All plans except for a Basic plan can use Solr. Solr is available to Sandbox site plans for developmental purposes, but Solr will not be available going live on a Basic plan.

| Plans         | Solr Support <Popover content="Available across all environments, including Multidevs." /> |
| ------------- | ---------------------------------- |
| Sandbox       | <span style="color:green">✔</span> |
| Basic         | <span style="color:red">❌</span>  |
| Performance   | <span style="color:green">✔</span> |
| Elite         | <span style="color:green">✔</span> |

## Using Solr with WordPress or Drupal

For installation instructions and additional details, see [Enabling Solr for WordPress](/docs/wordpress-solr), [Enabling Solr with Drupal 7](/docs/solr-drupal-7) or [Enabling Solr on Drupal 8](/docs/solr-drupal-8).

## Known Limitations of Pantheon's Solr Service

- Anything that takes more than 5 seconds to send to the Solr server to be indexed will timeout, which will block indexing. For example, large documents attached to Drupal nodes. In these cases, the developer must work with the content or code to exempt the nodes and/or files from being indexed. [Check Index and Batch sizes](#apache-solr-vocabulary) for possible scenarios or more time-outs.
- If you have a very large number of facets, the URLs generated by CURL may exceed length limits (may be fixed in search\_api).
-  Partial search, stopwords.txt, synonyms.txt, elevate.xml, etc. are not supported.
-  Drupal 7: When a Solr query is over 4000 characters and the method is changed from GET to POST, queries may fail. Known workarounds are to patch the module and set [`apachesolr_search_post_threshold` to a higher limit](https://github.com/pantheon-systems/drops-7/blob/master/modules/pantheon/pantheon_apachesolr/Pantheon_Apache_Solr_Service.php#L873) or to keep queries shorter.
- The search terms `in` and `or` are ignored, regardless of casing.
- Re-indexing may be required following any platform issue or event (server outage, service degradation). Check the [status page](http://status.pantheon.io) for current and past platform events.

## Alternatives to Pantheon's Solr Service

While Pantheon provides a stable, reliable, and basic Solr service, your individual site needs may require something more robust and customizable. In those cases, a dedicated hosted Solr service may be a better solution for your needs. Given that Solr can tolerate higher latency (one query per request vs hundreds of database queries), Solr servers do not need to be in the same data center to provide fast and responsive results.

Some customers have reported success using external Solr service providers for their Solr indexing:

 - [IndexDepot](https://www.indexdepot.com/en/). For more information, see [Using IndexDepot With Pantheon Sites](/docs/indexdepot/).
 - [OpenSolr](https://opensolr.com/)
 - [WebSolr](https://websolr.com/)

## Apache Solr Vocabulary

<DefList>

<Definition name="bias">

Allows certain parts of indexed items to influence the importance of search results. The higher the bias, the greater the influence; the range is 0.1 to 21.0.

</Definition>

<Definition name="core">

A core is a separate configuration and index using a single Solr instance. A core is created when the schema is posted. For more information, see [https://wiki.apache.org/solr/CoreAdmin](https://wiki.apache.org/solr/CoreAdmin)

</Definition>

<Definition name="document">

A document is similar to a database row, containing the contents of what is to be searched and whatever fields are associated with it, like title.

</Definition>

<Definition name="facet">

Search facets allow search results to be filtered; examples include seeing a list of potential filters and the count of matches for each filter on the left, like Amazon product searches.

</Definition>

<Definition name="index">

structure containing extracted keywords from a document for rapid search and retrieval, similar to a database table.

</Definition>

<Definition name="score">

calculated relevance of matches influenced by bias, represented as a float.

</Definition>

<Definition name="schema.xml">

Contains details about the fields that documents can contain, and how those fields are handled when adding documents to the index or querying those fields. Must be posted using the pantheon_apachesolr module before indexing and searching will work. For more information, see [https://wiki.apache.org/solr/SchemaXml](https://wiki.apache.org/solr/SchemaXml).

</Definition>

</DefList>

#### Check Index and Batch Sizes

Are you only indexing only 50 items at a time and wondering why hundreds of new content nodes generated in the last hour aren't being indexed? Numbers are important here. If you need to increase the number of items being indexed with each Solr indexing run, feel free to do so. However, don't get ridiculous here: setting 1000 items to be indexed per run could cause a page timeout. If a specific request times out, that could be because it's trying to POST too much data at once; try reducing the quantity of items being indexed per batch and see if that allows the items to be indexed.

### Apache Spatial Search on Pantheon

Pantheon's Solr configuration does not support geospatial indexing and searching and there are currently no plans to add it.
As an alternative, there are several external Solr service providers that do support spatial searching. Pantheon doesn’t do any blocking/filtering, so you’re welcome to use an externally hosted Solr index – and in a case where you’re looking for a more complex configuration, that might be optimal.

### Specify Query Parsers to Allow Fields
Pantheon's Solr configuration uses the [DisMax](https://cwiki.apache.org/confluence/display/solr/The+DisMax+Query+Parser) query mode by default. This parser is great for user-entered text because it's forgiving and similar to Google's parser, but it does not recognize `field:value` syntax.

The solution is to specify the parser in the request. Both the [Lucene](https://cwiki.apache.org/confluence/display/solr/The+Standard+Query+Parser) and [eDisMax](https://cwiki.apache.org/confluence/display/solr/The+Extended+DisMax+Query+Parser) parsers support the required field:value syntax. You can add them to a query by adding `&defType=lucene` or `&defType=edismax` to the end. For example:
```
/select?q=ts_ol_full_text:property&fl=label,bundle,ts_ol_full_text&wt=json&defType=edismax
```
For details on supported query parsers, see [Query Syntax and Parsing]( https://cwiki.apache.org/confluence/display/solr/Query+Syntax+and+Parsing) and [Common Query Parameters](https://cwiki.apache.org/confluence/display/solr/Common+Query+Parameters#CommonQueryParameters-ThedefTypeParameter).

### Supported Search Components

The following Apache Solr search components are supported:

* SpellCheckComponent
* TermVectorComponent
* TermsComponent