__oson__ Language    
==========    
    
Abstract    
--------    
    
__oson__ is a micro-language to describe the schema of JSON. It allows you to write down the JSON and the schema of JSON inside only one file(with annotation). oson is a subset of JSON-Schema, so it can be compiled to JSON-schema, it is inspired by "orderly" and annotation.    
    
    
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
    
An exhaustive list of the differences between JSONSchema, orderly and __oson__ is below.    
    
## A Non-normative Tutorial    
    
A collection of Non-normative specification of __oson__:    
    
### case sensitivity    
    
In general, "annotation type" is case sensitiv, always use upper case for type.    
    
### comments and whitespace    
    
Early JSON doesn't support comments, so in general __oson__ should also not support line comments, but annotation supports comments, comments are initiated with '//' at the end of annotation.    
Annotation doesn't allow whitespace(except [] {})!!! these are examples:    
        
    "tom@Strin g" //invalid    
    "tom@String [1,3]" //invalid    
    "tom@String[1, 3]" //this is valid    
    
### value format    
key/value pair is the basic entry in JSON, key and value should always be __String__!!!    
        
    "key1":"123@Number[100,200]//this is a Number, but you need to change it to string."    
    "key1":"true@Boolean//this is a Boolean, but you need to change it to string."    
    
Always make sure that after adding Annotation to the original JSON, the new file is again a JSON.    
    
### common properties    
    
From the JSONSchema specification, four options exist which apply to all data types:    
    
The `optional` property indicates a value which is not required in a conformant JSON instance.  Optional values are represented in __oson__ with a trailing question mark in __key side__:    
    
    "key1?":"value1@String"    //use '??' to escape(to represent the real "?")    
    //orderly: string key1?;    
    
The `requires` property indicates a that if a value is present in the instance JSON, another named value must also be present.  In __oson__ a requirement on another type is expressed by placing the property name (optionally quoted) enclosed in angle brackets at the end of a type definition:    
  
    //state and zip are required:    
    {    
        "\<state\>":"@String",    
        "\<zip\>":"@String"    
    }    
    //orderly: string town <state,zip>;    
        
The `enum` propery specifies a set of allowable values for a key in the json document.    
In __oson__ :    
        
    "mood": "@String{'happy', 'sad', 'meh'}"   //note: inside "{}" only '' is allowed.     
    //orderly: string mood [ "happy", "sad", "meh" ];    
        
    or "mood": "@Enum{'happy', 'sad', 'meh'}"    
    //orderly: Any mood [ "happy", "sad", "meh" ];    
        
    "secretOfLife": "@integer{7,42}"   //note: "@integer[7,42]" means {7, 8, 9,...,42}    
    //orderly: integer secretOfLife [ 7, 42 ];    
    
In a JSONSchema document the `default` property specifies a default value for a property.      
This is different with the `example` property, see [JSON Schema](https://json-schema.org/), the value before annotation in __oson__ is example value, not default value!!!    
In __oson__ :    
    
    "mood": "@String{'happy', 'sad', 'meh'}='happy'"    
    //orderly: string mood [ "happy", "sad", "meh" ] = "happy"; # optimistically default to "happy"    
    
    
### String types    
    
Strings are specified in __oson__ using the `@String` inside annotation. Square brackets support "minLength" and "maxLength" properties.    
        
    "login": "loginExample@String[4,12]"    
    //orderly: string{4,12} login;    
    
Like Orderly, omission of a specification of either minimum or maximum is allowed.    
        
    "login": "loginExample@String[4,]"    //use '@@' to escape(to represent the real "@")    
    
Regular expressions are supported in JSONSchema for string values. In __oson__:    
    
    "mood": "moodExample@String/^((happy)|(sad)|(meh))$/"    
    //orderly: string mood /^((happy)|(sad)|(meh))$/;    
    
### Number & Integer types    
    
Numbers are specified in __oson__ using the `@Number` and `@Integer` inside annotation. Square brackets support "minimum" and "maximum" properties. In __oson__:    
    
    "numNum": "0.06@Number[0.02, 0.98]"    
    //orderly: number{0.02, 0.98} numNum;    
    "rating": "6@Integer[0,10]"    
    //orderly: integer{0,10} rating;    
    
### Boolean types    
    
Boolean types are represented in __oson__ using the `@Boolean` inside annotation.    
    
    "iShouldStay":"false@Boolean"    
    //orderly: boolean iShouldStay;    
    
### Object types    
    
The same Syntax as JSON, use `{}`.    
The star `*` property corresponds to the "additionalProperties" in JSON Schema.     
In __oson__ :    
    
    {    
        "obj1":{    
            "foo":"@String"    
            "*":"@*"   // === "additionalProperties": true    
        }    
    }    
    //orderly:     
    //obj1 {    
    //    string foo;    
    //    # whatever other properties you want, thanks to that star    
    //}*;    
    
    
### array types    
    
The same Syntax as JSON, use `[]`, annotation of one data is enough for one-type-array.    
        
    "weights":[    
        "0.5@Numbers[0.00, 1.00]",    
        "0.1",    
        "0.4"    
    ]    
    
    //orderly:     
    //array [    
    //    numbers{0.00, 1.00};    
    //] weights; # an array of floating point weights between 0 and 1.    
    
"tuple typing": annotation of one data for each type is enough.    
    
    "artificial":[    
        "13@Integer",    
        "str@String",    
        "16",    
        "1.6@Number"    
    ]    
    
    //orderly:     
    //array {    
    //    integer;    
    //    string;    
    //    number;    
    //} artificial;    
    
    //JSON Schema:    
    // "myarray": {    
    //      "type": "array",    
    //      "items": [    
    //        {"type": "integer"},    
    //        {"type": "string"},    
    //        {"type": "number"}    
    //      ]    
    // }    
    
Finally, when tuple typing is used, the star `@*` member may be used to pass the "additionalProperties" in JSON Schema.    
    
    "intFollowedByWhatever":[    
        "@Integer"    
        "@*"    
    ]    
    
    //orderly:     
    //array { integer; }* intFollowedByWhatever;     
    
Finally, array types also support range semantics, corresponds to "minItems/maxItems" in JSON Schema.    
    
    "myArrayOfSmallInts":[    
        "@Integer"    
        "@String"    
        "@[0,10]"    
    ]    
        
    //orderly:     
    //array { integer; string;} {0,10} myArrayOfSmallInts;    
    
### handling "additional properties" in arrays and objects    
    
False by default.    
True if `@*` member exists in arrays or `*`:`@*` property exists in objects.    
    
### null types    
    
The null type in JSONSchema specifies a value that must be null.  Null types are represented in __oson__ using the `@Null` inside annotation.    
    
    "likeAir": "@Null"    
    //orderly: null likeAir;    
    
As explained in the JSONSchema proposal, `null` is useful "mainly for purpose of being able use union types to define nullability".  For example:    
    
    "suffix":"@(String{'Sr.', 'Jr.', 'III'})|(Null)"    
    
    //orderly: union {    
    //    string [ "Sr.", "Jr.", "III" ];    
    //    null;     
    //} suffix;    
    
### any types    
in __oson__ using the `@Any` inside annotation, or omitted.    
    
    "notes": "noteA@Any"    
    "notes": "noteA"    
    
    //orderly: any notes;    
    
### type union(Type A or Type B)    
    
Combine different types to one type. Every type muss be inside a parentheses.    
    
    "myUnion":"@(String)|(Number)" //the type of myUnion Attribute will be String or Number    
    
    //orderly:     
    //union {    
    //    string;    
    //    number;    
    //} myUnion;    
    
A key syntactic feature to note is the supported (required?) ommission of property names where they would be meaningless.    
    
### extensions    
TODO: () [) (]    
    
    
### Grammar    
  
    osonSchema  
        osonValue  
  
    osonValue  
        osonObject  
        osonArray  
        annotatedString  
  
    osonObject  
        '{' '}'  
        '{' osonMembers '}'  
  
    osonMembers  
        osonMember  
        osonMember ',' osonMembers  
  
    osonMember  
        annotatedKey ':' osonValue  
  
    osonArray  
        '[' ']'  
        '[' osonElements ']'  
  
    osonElements  
        osonValue  
        osonValue ',' osonElements  
  
    annotatedKey  
        annotatedKeyString  
        string  
  
    annotatedString  
        openannotatedValueString annotation '"'  
        string  
  
    annotation  
        osonType suffix  
  
    osonType  
        unionType  
        basicType  
      
    unionType  
        '(' basicType ')' '|' unionType  
        '(' basicType ')'  
      
    basicType  
        "Integer" optionalRange  
        "Number" optionalRange  
        "Boolean"  
        "Null"  
        "Any"  
        "String" optionalRange optionalPerlRegex  
        "JSON" optionalExtraProperties  
        "*"     #this is an additional properties @*.  
  
    suffix  
        optionalEnumValues optionalDefaultValue  
  
    optionalExtraProperties  
        '`' json_object '`'  
        # nothing  
  
    optionalRange  
        '[' json_number ',' json_number ']'  # math notation  
        '[' json_number ',' ']'  
        '[' ',' json_number ']'  
        '[' ',' ']'                          # meaningless, yes.  
        # nothing  
  
    optionalPerlRegex  
        '/' ([^/]|\/) '/'   # a Perl 5 compatible regular expression  
        #nothing    
  
    optionalExtraProperties  
        '`' json_object '`'  
        # nothing  
  
    optionalEnumValues  
        '{' elements '}'    # math notation  
        '{' '}'             # math notation  
        # nothing    
  
    optionalDefaultValue  
        '=' json_value  
        # nothing    
      
  
    ----------------token table----------------  
    annotatedKeyString:= {DBL_QUOTE}{CHARS}{KEY_ANNOTATION_SYMBOL}{DBL_QUOTE}  
    openannotatedValueString:= {DBL_QUOTE}{VALUE_ANNOTATION_SYMBOL}|{DBL_QUOTE}{CHARS}{VALUE_ANNOTATION_SYMBOL}  
    string:= {DBL_QUOTE}{DBL_QUOTE}|{DBL_QUOTE}{CHARS}{DBL_QUOTE}  
    number:= NUMBER  
    whiteSpace:= [ \t\n]+  
    ------define-----  
    KEY_ANNOTATION_SYMBOL       [\?]  
    VALUE_ANNOTATION_SYMBOL     [@]  
    DIGIT1to9                   [1-9]  
    DIGIT                       [0-9]  
    DIGITS                      (?:{DIGIT}+)  
    INT                         (?:{DIGIT1to9}{DIGITS}|{DIGIT}|"-"{DIGIT1to9}{DIGITS}|"-"{DIGIT})  
    FRAC                        (?:\.{DIGITS})  
    EXP                         (?:{E}{DIGITS})  
    E                           [eE][+-]?  
    HEX_DIGIT                   [0-9a-f]  
    NUMBER                      (?:{INT}{FRAC}{EXP}|{INT}{EXP}|{INT}{FRAC}|{INT})  
    UNESCAPEDCHAR               [ -!#->A-\[\]-~]  
    ESCAPEDCHAR                 \\["\\bfnrt/]  
    UNICODECHAR                 \\u{HEX_DIGIT}{HEX_DIGIT}{HEX_DIGIT}{HEX_DIGIT}  
    CHAR                        {UNESCAPEDCHAR}|{ESCAPEDCHAR}|{UNICODECHAR}|"??"|"@@"  
    CHARS                       {CHAR}+  
    DBL_QUOTE                   ["]  
  
      
  
  
  
    ----------------------------------------    
    ---------- [The JSON Grammar] ----------    
    ----------------------------------------    
        
    json_object    
        {}    
        { members }     
    members    
        pair    
        pair , members    
    pair    
        json_string : json_value    
    json_array    
        []    
        [ elements ]    
    elements    
        json_value    
        json_value , elements    
    json_value    
        json_string    
        json_number    
        json_object    
        json_array    
        'true'    
        'false'    
        'null'    
        
    ----------------------------------------    
        
    json_string    
        ""    
        " chars "    
    chars    
        char    
        char chars    
    char    
        any-Unicode-character-    
            except-quote-or-backslash-or-    
            control-character    
        \" #double quote (")    
        \\    
        \/    
        \b    
        \f    
        \n    
        \r    
        \t    
        \u four-hex-digits     
    json_number    
        int    
        int frac    
        int exp    
        int frac exp     
    int    
        digit    
        digit1-9 digits    
        - digit    
        - digit1-9 digits     
    frac    
        . digits    
    exp    
        e digits    
    digits    
        digit    
        digit digits    
    e    
        e    
        e+    
        e-    
        E    
        E+    
        E-    
    
## Author    
Shutao Shen  --  04.04.2020