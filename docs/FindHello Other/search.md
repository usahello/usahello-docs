# Search

Search and autocomplete for the application are handled by Haystack and Solr. Haystack is the Django implementation and interface for the search-backend which in our case is Solr. Solr uses Apache Lucene behind the scenes. We have Solr set up as a stand-alone server on AWS.

## Solr in Production

How Solr is set up for production is covered  in the [Deployment Solr Setup Guide](../deployment/solr-setup.md). This AWS instance is currently hidden from public internet traffic. 

## Solr in Development

During development, the Django API uses a dummy search backend instead of a Solr instance to make life easier. This means you can ignore Solr in development if you want to.

That said, you can set up a Solr instance fairly easily on MacOS if you need to test it. Install Solr via the [Solr Setup Guide](../development/solr-setup.md) and then update your `.env` variables to make use of it locally:

```
HAYSTACK_ADMIN_URL="http://127.0.0.1:8983/solr/"
HAYSTACK_URL="http://127.0.0.1:8983/solr/mycore/"
HAYSTACK_ENGINE="haystack.backends.solr_backend.SolrEngine"
```

## Updating the index

Any time you make changes to the structure of the index (in other words, updating `location/search_indexes.py` for example) you need to update the Solr configurations on both staging and production. Put another way, Haystack doesn't automatically do this for you - you have to manually do it yourself.

A good workflow for this is to:

- Make the changes to the `schema.xml` and/or `solrconfig.xml` locally first
- Test on your local version of Solr
- Commit `schema.xml` and/or `solrconfig.xml` to the `/etc/` folder so that these are always the most up to date version
- Copy these comitted versions to the production Solr instance