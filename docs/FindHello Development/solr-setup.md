# Solr Setup

## Install Solr

Use brew to install solr: 

```bash
brew install solr
```

This places the Solr home directory at `/usr/local/Cellar/solr/8.2.0/server/solr/`

Start it running locally:

```
solr start
```

Create a new core:

```bash
solr create -c findhello-local -d _default
```

## Setup Django

Haystack should already be installed in your local Django set up via the `requirements` files, but if not:

```bash
pip install django-haystack
```

Update your `.env` vars to make sure you are using the Solr backend and not the dummy search backend that is enabled by default:

```
HAYSTACK_ADMIN_URL="http://127.0.0.1:8983/solr/"
HAYSTACK_URL="http://127.0.0.1:8983/solr/findhello-local/"
HAYSTACK_ENGINE="haystack.backends.solr_backend.SolrEngine"
```

Note that the `URL` points to the core, while the `ADMIN_URL` is just the default admin panel.

## Install existing `schema.xml` and `solrconfig.xml`

Assuming you haven't made any changes to the search indexes, you can install the `schema.xml` file and `solrconfig.xml` file that are saved in the repo's `etc` folder:

```
cp etc/solrschema.xml /usr/local/Cellar/solr/8.2.0/server/solr/findhello-local/conf/schema.xml
cp etc/solrconfig.xml /usr/local/Cellar/solr/8.2.0/server/solr/findhello-local/conf/solrconfig.xml
```

Then reload the core:

```bash
curl 'http://127.0.0.1:8983/solr/admin/cores?action=RELOAD&core=findhello-local&wt=json'
```

and rebuild the index:

```bash
python manage.py rebuild_index
```

## Updating index (and therefore `schema.xml` and `solrconfig.xml`)

If you have made any changes to the `search_indexes.py` file, you will need to update the `schema.xml` and `solrconfig.xml` locally (and on the staging/production cores).

After making your changes, run:

```
python manage.py build_solr_schema -c /usr/local/Cellar/solr/8.2.0/server/solr/findhello-local/conf/
```

then copy the updated `xml` files to your repo:

```bash
cp /usr/local/Cellar/solr/8.2.0/server/solr/mycore/conf/schema.xml etc/solrschema.xml
cp /usr/local/Cellar/solr/8.2.0/server/solr/mycore/conf/solrconfig.xml etc/solrconfig.xml
```

Reload the core:

```bash
curl 'http://127.0.0.1:8983/solr/admin/cores?action=RELOAD&core=findhello-local&wt=json'
```

Now rebuild the index and everything should be ready to go:

```bash
python manage.py rebuild_index
```

Make sure to commit the changes in `etc` and push them. 

**You also need to make sure to update these files on staging and production so see the deployment notes on Solr**

## Troubleshooting

### Error regarding `currency.xml`

After installing the schema with Haystack, I was noticing this error in the Solr admin panel

> org.apache.solr.common.SolrException:org.apache.solr.common.SolrException: Could not load conf for core findhello-local: Error while parsing currency configuration file currency.xml

This is due to the default haystack schema that it generates includes fields for currency, for example:

```xml
<dynamicField name="*_c"   type="currency" indexed="true"  stored="true"/>
...
<fieldType name="currency" class="solr.CurrencyField" precisionStep="8" defaultCurrency="USD" currencyConfig="currency.xml" />
...
```

So delete them! Or you can add an example `currency.xml` to the core's config folder:

https://raw.githubusercontent.com/apache/lucene-solr/master/solr/example/files/conf/currency.xml

### Error regarding `QueryElevationComponent`

If you see this in the Solr admin:

> org.apache.solr.common.SolrException:org.apache.solr.common.SolrException: Error initializing QueryElevationComponent

Delete the following from the `solrconfig.xml`:

```xml
<!-- Query Elevation Component

       http://wiki.apache.org/solr/QueryElevationComponent

       a search component that enables you to configure the top
       results for a given query regardless of the normal lucene
       scoring.
    -->
  <searchComponent name="elevator" class="solr.QueryElevationComponent" >
    <!-- pick a fieldType to analyze queries -->
    <str name="queryFieldType">string</str>
    <str name="config-file">elevate.xml</str>
  </searchComponent>

  <!-- A request handler for demonstrating the elevator component -->
  <requestHandler name="/elevate" class="solr.SearchHandler" startup="lazy">
    <lst name="defaults">
      <str name="echoParams">explicit</str>
    </lst>
    <arr name="last-components">
      <str>elevator</str>
    </arr>
  </requestHandler>
```
