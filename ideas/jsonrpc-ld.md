# concept: jsonLD style-rpc
assumption: this proposed web-thing-protocol (WoTP) is an **application axiomatization** of *domain axiomatization*:[ The WoT Thing Description](https://www.w3.org/TR/wot-thing-description11/)
## goals
1. stay as close as possible to the semantics & ontology of
   [The WoT Thing Description: Hypermedia Controls Vocabulary Definitions](https://www.w3.org/TR/wot-thing-description11/#sec-hypermedia-vocabulary-definition) 
2. maintain compatibility or easy conversion with json-rpc
3. expect to be used with protobuf to convert between json and binary

## syntax
```
 "-->" = Request
 "<--" = Response 
```
## Request messages fields
| Request messages fields |    Json-prc Request object members   | proposed WoTP ontology names |
| ----------------------: | :--------------------------- | :--- |
| type                    |   indicated by member names   | |
| operation               |       method       | op|
| thingID                 |    param.thingID    | thingID|
| name                    |     param.name      | affordanceID |
| input                   |     param.input     | params |
| correlationID           | param.correlationID | subscriptionId |
| senderID                |   param.senderID   |  senderID  |
| messageID               |         id        |     msgID|

example 1:
```
--> { 
        "jsonrpc": "2.0", 
        "method": "string", 
        "params": {
            "thingID": "string", 
            "name": "string", 
            "input": "any", 
            "correlationID": "string", 
            "senderID": "string"
        },
        "id": "string"
    }
```
## Notification messages fields
same as a Request except "id" is moved to "params" as "messageID"
```
{
        "jsonrpc": "2.0",
        "method": "string", 
        "params": {
            "thingID": "string",
            ...
+++             "messageID": "string
        },
---        "id": "string"
}

====== becomes ====== 

{ 
        "jsonrpc": "2.0",
        "method": "string", 
        "params": {
            "thingID": "string",
            ...
            "messageID": "string
        }
}

```
## Response  messages fields
| Response messages fields |    Json-prc Request object members   | proposed WoTP ontology names |
| ----------------------: | :--------------------------- | :--- |
| type                    |   indicated by member names   | |
| status                 |    result.status    | status |
| thingID                 |    result.thingID    | thingID|
| name                    |     result.name      | affordanceID
| output	                   |     result.output     | results |
| error	                   |     result.error    | errors |
| received           | result.received | rxTs|
| updated                |   result.updated    | updatedTs|
| correlationID           | result.correlationID | corrID |
| messageID               |         id         | msgId|


example 2:
```
<-- { 
        "jsonrpc": "2.0", 
        "method": "string", 
        "result": {
            "correlationID": "string",
            "error": "string",  
            "name": "string", 
            "output": "any",  
            "received": "string", 
            "status": "string", 
            "thingID": "string", 
            "updated": "string"
        },
        "id": "string"
    }
```

### this proposed web-thing-protocol (WoTP)
assumptions/ideas

#### conversion of json-rpc  member name
| Json-prc Request object members  | proposed WoTP ontology names |
| ----------------------: | :--------------------------- | 
| jsonrpc                    |   wotp   | 
| method                 |    operation    

final Json-rpc objects, pre-conversion to the ArrayBuff of a websocket binary payload

example 3:
```
--> { 
        "wotp": "<version>", 
        "operation": "string", 
        "params": {
            "thingID": "string", 
            "name": "string", 
            "input": <any>, 
            "correlationID": "string", 
            "senderID": "string", 
            "messageID": "string"
        },
        "id": "string"
    }
    
<-- { 
        "wotp": "<version>", 
        "operation": "string", 
        "result": {
            "correlationID": "string",
            "error": "string",  
            "name": "string", 
            "output": "any",  
            "received": "string", 
            "status": "string", 
            "thingID": "string", 
            "updated": "string"
        },
        "id": "string"
    }

====== notification example ====== 
  
--> { 
        "jsonrpc": "2.0",
        "method": "string", 
        "params": {
            "thingID": "string",
            ...
            "messageID": "string
        }
}
```

