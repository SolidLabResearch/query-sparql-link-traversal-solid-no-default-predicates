# query-sparql-link-traversal-solid-no-default-predicates

A [@comunica/query-sparql-link-traversal-solid](https://www.npmjs.com/package/@comunica/query-sparql-link-traversal-solid) engine,
configured to **not follow any default predicates** (such as `http://www.w3.org/2000/01/rdf-schema#seeAlso`).

It allows you

* to use an engine with this behavior, without the need to provide your own configuration file
* and even to apply it in a browser environment.

To use it, see below an example code snippet.

## Installation

In your application's project root folder, execute:

```bash
npm install query-sparql-link-traversal-solid-no-default-predicates
```

## Usage within application

This is an example demonstrating how to use this engine to retrieve datasources from an index file containing triples `?s <http://example.com/hasDataSource> ?source`.

```javascript
// This works in node AND in a browser (when packaged by vite for example).
import { QueryEngine } from 'query-sparql-link-traversal-solid-no-default-predicates';
const engine = new QueryEngine;

async function getSourceList() {
  const bindingsStream = await engine.queryBindings(
    'SELECT DISTINCT ?source WHERE {?s <http://example.com/hasDataSource> ?source.}',
    {
      sources: ['http://example.com/index-file-with-triples-listing-sources'],
      lenient: true
    });
  let sources = [];
  const bindings = await bindingsStream.toArray();
  for (let binding of bindings) {
    sources.push(binding.get('source').value)
  }
  return sources;
}

const datasources = await getSourceList();
console.log(datasources);
```

### Note

Replacing the top 3 lines in above example with:

```javascript
// This only works in node (supposing ./config.json has the same contents as query-sparql-link-traversal-solid-no-default-predicates's engine-default.js),
// because QueryEngineFactory is not available in browser environments
import { QueryEngineFactory } from '@comunica/query-sparql-link-traversal-solid';
const engine = await new QueryEngineFactory().create({ configPath: './config.json', });
```

would not work in a browser environment.
