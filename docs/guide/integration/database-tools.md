# Database tool

Storefront API provides you the unified tooling for managing the embedded Elastic Schema. This tool lets you to create new indexes or apply modified index mappings to the exisiting one's. ElasticSchema is provided from the [modules](../modules/introduction.md) level.

## The need for database tool

After more extensive data operations, like custom imports using [mage2vuestorefront](https://github.com/DivanteLtd/mage2vuestorefront) or [magento1-vsbridge](https://github.com/DivanteLtd/magento1-vsbridge), there is a need to reindex the Elasticsearch and set up the proper metadata for fields.

The main reason you’ll know you must reindex the database is the following error you get from the `storefront-api` console:

```json
Error: {"root_cause":[{"type":"illegal_argument_exception","reason":"Fielddata is disabled on text fields by default. Set fielddata=true on [created_at] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."}],"type":"search_phase_execution_exception","reason":"all shards failed","phase":"query","grouped":true,"failed_shards":[{"shard":0,"index":"vue_storefront_catalog_1521776807","node":"xIOeZW2lTwaprGXh6YLyCA","reason":{"type":"illegal_argument_exception","reason":"Fielddata is disabled on text fields by default. Set fielddata=true on [created_at] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."}}]}
```

In this case, there is a db tool inside your local `storefront-api` installation to the rescue.

## Re-indexing an existing database

Please go to `storefront-api` directory and run:

```bash
docker exec -it sfa_app_1 yarn db7 rebuild
```

This command will:

- Reindex your currently set (in the `config/local.json` config file) Elasticsearch index to temp-one.
- Put the right Elasticsearch mappings on top of the temp index.
- Drop the original index.
- Create the alias with original name to the temp one so you can use the original name without any reference changes.


You can specify different (than this set in `config/local.json`) index name by running:

```bash
docker exec -it sfa_app_1 yarn db7 rebuild -- --indexName=custom_index_name
```

## Creating the new index

If you want to create a new, empty index please run:

```bash
docker exec -it sfa_app_1 yarn db7 new
```

This tool will drop your current index and create a new, empty one with all the metafields set.

You can specify different (than this set in `config/local.json`) index name by running:

```bash
docker exec -it sfa_app_1 yarn db7 rebuild -- --indexName=custom_index_name
```

## Changing the index structure / adding new fields / changing the types

If you want to extend the Elasticsearch data structures or map some particular field types, for example, after getting kind of this error:

```
[{"type":"illegal_argument_exception","reason":"Fielddata is disabled on text fields by default. Set fielddata=true on [created_at] in order to load fielddata in memory by uninverting the inverted index. Note that this can however use significant memory. Alternatively use a keyword field instead."}]
```

Please do change the ES schema by modifying the schema provided by particular [module](../modules/introduction.md)

The format is compliant with [ES DSL for schema modifications](https://www.elastic.co/blog/found-elasticsearch-mapping-introduction)

After the changes, run the following indexing command:

```bash
docker exec -it sfa_app_1 yarn db7 rebuild
```
