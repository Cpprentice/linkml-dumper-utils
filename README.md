from pydantic import BaseModel

# LinkML Dumper Utils

This is a small helper package to get the dumping with linkml models more aligned
with the practices of pydantic and Python dataclasses.
Namely, using model_dump_* and as* methods.
Moreover, some tooling to directly add linkml schema objects to your classes is included
to be able to produce different serializations out of the box.

At the moment the serialization is supported for both JsonLD and RDF.
However, the JSON LD implementation is not actually using the linkml runtime dumpers,
but replicates some of its functionality but in a more convenient way.

## Example usage

```python
from pydantic import BaseModel
from linkml_dumper_utils.pydantic import PydanticJsonldMixin

class MyClass(BaseModel, PydanticJsonldMixin, context_file_path='myContext.json'):
    name: str


obj = MyClass(name='Bob')

jsonld = obj.model_dump_jsonld()
```

Considering the context of `myContext.json` looks like the following

```json
{
    "foaf": "http://xmlns.com/foaf/0.1/",
    "dct": "http://purl.org/dc/terms/",
    "Person": "foaf:Person",
    "name": "dct:title"
}
```

The jsonld object above would be a Python dict looking like this:

```json
{
    "@context": {
        "foaf": "http://xmlns.com/foaf/0.1/",
        "dct": "http://purl.org/dc/terms/",
        "Person": "foaf:Person",
        "name": "dct:title"
    },
    "@type": "Person",
    "name": "Bob"
}
```

## Usage for pydantic

There are several ways how this package can be used to interact with both existing
pydantic models that can not be modified and your own pydantic models that you can
simply update to reflect what you need.

### Pydantic model using mixin via inheritance

The basic usage would look like this:

```python
from pydantic import BaseModel
from linkml_dumper_utils.pydantic import PydanticJsonldMixin

class MyClass(BaseModel, PydanticJsonldMixin, context_file_path='myContext.json'):
    ...
```

The context for a class can be specified in three ways:

- context_file_path
- context_string (also works as context URL)
- context