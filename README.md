__oson__ Language    
==========    
    
Abstract    
--------    
    
__oson__ is a micro-language to describe the schema of JSON, sub-language of UMF. It allows you to write down the JSON and the schema of JSON inside only one file(with annotation). oson is a subset of JSON-Schema, so it can be compiled to JSON-schema, it is inspired by "orderly" and annotation.    
A commonJS compiler can be found here: [oson.js](https://github.com/sstshenshutao/oson.js)    
    
A subset of JSONSchema    
----------------------    
    
__oson__ attempts to provide a quickest way to modify a JSON to a JSON Schema.    
Usually, if we want to write a JSON Schema based on a JSON format, we need to create another new file to write down all of the rules, attributes and types in the JSON to finish a JSON Schema. This process is too boring, disgusting, and stupid.     
There are some tools to infer the JSON Schema with a JSON file, like [JSONSchema.Net](https://jsonschema.net/home). Unfortunately in some cases, the schemas are not correctly inferred, the restriction is sometimes too weak or too strong for the users.     
## Why __oson__, we already have JSON-Schema:    
Mostly, if we transfer the data with JSON format, we usually don't have JSON-Schema, what we have is an example. With this example, we can write a JSON-Schema to rule the JSON.    
### JSON Schema generate tools:    
We use [JSONSchema.Net](https://jsonschema.net/home) to show the problem:    
Consider of this situations examples:  
  
    {"notes":"this is a note"}  // the notes can also be "null"  
  
After inferring the schema, we got:  
  
    {  
        "required": [  
            "notes"  
        ],  
        "properties": {  
            "notes": {  
                "type": "string",  
                "examples": [  
                    "this is a note"  
                ]  
            }  
        }  
    }  
  
which means the `Note` must be a property of type `String`, it can not be optional or other types. What about type `Number`, `Null`, or even it is optional?    
With __oson__ you can define you JSON precisely, without the above ambiguities.    
      
    {"notes":"this is a note@(String[4,100])|(Null)|(Number)"}   
    // this means: Null, number or a string with length 4 to 100 are allowed.  
  
So an language with annotation is a better way to achieve this goal.    
  
## __oson__  
__oson__ provide a quickest way: annotation on a JSON file. A valid __oson__ file is a annotated JSON file. With __oson compiler__, this JSON can be converted to a JSON Schema.    
    
The differences between __oson__ and __Orderly__ :    
Language Description: __oson__ is not powerful as __Orderly__, only some of the features in __Orderly__ will be implemented.    
Example Information: __oson__ combine the example data with the Schema.    
Language Format: __oson__ is a pure JSON format.    
Learning Cost: __oson__ is as easy as "Markdown"    
Math Notations: __oson__ uses math notations: {} for set, [] for range, whereas __Orderly__ uses programming notations:{} for range, [] for array.    

for more details, see doc.md  
  
