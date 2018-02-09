## marshmallow-avroschema: Avro Schema formatting with marshmallow

 marshmallow-avroschema translates marshmallow schemas into
 Avro 1.8.2 compliant schema. See https://avro.apache.org/docs/1.8.2/

#### Why would I want my schema translated to JSON?

What are the use cases for this? Let's say you have a
marshmallow schema in python, but you want to render your
schema as a form in another system (for example: a web browser
or mobile device).

#### Installation

```
pip install marshmallow-avroschema
```

#### Simple Example

```python
from marshmallow import Schema, fields
from marshmallow_avroschema import AvroSchema

class UserSchema(Schema):
    username = fields.String()
    age = fields.Integer()
    birthday = fields.Date()

user_schema = UserSchema()

avro_schema = AvroSchema()
avro_schema.dump(user_schema).data
```
Yields:
```python
# TODO: fill in with Avro Schema data output
```


#### Custom Type support

Simply add a `_avroschema_type_mapping` method to your field
so we know how it ought to get serialized to Avro Schema.

A common use case for this is creating a dropdown menu using
enum (see Gender below).


```python
class Colour(fields.Field):

    def _avroschema_type_mapping(self):
        return {
            'type': 'string',
        }

    def _serialize(self, value, attr, obj):
        r, g, b = value
        r = hex(r)[2:]
        g = hex(g)[2:]
        b = hex(b)[2:]
        return '#' + r + g + b 

class Gender(fields.String):
    def _avroschema_type_mapping(self):
        return {
            'type': 'string',
            'enum': ['Male', 'Female']
        }


class UserSchema(Schema):
    name = fields.String(required=True)
    favourite_colour = Colour()
    gender = Gender()

schema = UserSchema()
avro_schema = AvroSchema()
avro_schema.dump(schema).data
```
