# proposal draft/discussion: web-thing-protocol (WoTP): json-rpc system extension
this concept/discussion/ideation is a child of [issue #34](https://github.com/w3c/web-thing-protocol/issues/34). It was moved for the following reasons:

- @RobWin, https://github.com/w3c/web-thing-protocol/issues/34#issuecomment-2572721125

  > @unit9a I think that the JSON-RPC discussion should be moved out of this issue. Perhaps it could continue in the WoT Discord channel until someone expresses interest in creating a dedicated Sub-Protocol Community Group.

-  @benfrancis, https://github.com/w3c/web-thing-protocol/issues/34#issuecomment-2575181751
    > I can definitely see the similarity with @hspaay's proposal (except that in JSON-RPC notifications usually work in the opposite direction). I can certainly imagine something that looks like the WoT Scripting API implemented over JSON-RPC, but I'm not personally interested in that as a design for similar reasons to those given above. I would rather not abstract everything as a method.

    > JSON-RPC can be used used as a sub-protocol of both HTTP and WebSockets so arguably does fall within that scope, it's just not something I'm personally interested in. It could make an interesting [protocol binding template](https://www.w3.org/TR/wot-binding-templates/) though.

this issue was created because the discord link on[ w3c Web Thing Protocol Community Group](https://www.w3.org/community/web-thing-protocol/) did not work for me at the time of posting.

## goals:

1. comply with Json-rpc v2.0 _AND_ message types defined in
   [Requirement: Proposal (2) for standardized message envelope for requests, responses and notifications](https://github.com/w3c/web-thing-protocol/issues/34#issue-2754869639).

2. be fully browser & Deno.js compatible
3. (low priority/personal) explore the use of knowledge graphs to generate
   documentation by creating a web-thing-protocol (WoTP) **application
   axiomatization** of _domain
   axiomatization_:[The WoT Thing Description](https://www.w3.org/TR/wot-thing-description11/)

## Json-rpc v2.0 system extension requirements

rpc spec says that it can be extended by prefixing method names with "rpc."

source:

> [8 Extensions <br>
Method names that begin with **rpc.** are reserved for system extensions, and MUST NOT be used for anything else. Each system ?> extension is defined in a related specification. All system extensions are OPTIONAL.](https://www.jsonrpc.org/specification#extensions)

### resolution: proposed "rpc.wotp" method namespace

all method names must use one of the following equivalent formats.

| description                                                                                                                                                    | format                                             | examples in json                                                                                                                    |
| :------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------- |
| non-extended json-rpc                                                                                                                                          | "rpc.wotp/" + method name                          | {method: "rpc.wotp/invokeAction"}                                                                                                   |
| non-extended json-rpc + explicit version                                                                                                                       | "rpc.wotp/" + "v" + protocol version + method name | {method: "rpc.wotp/v0.1/invokeAction" }                                                                                             |
| extended json-rpc <br> - wotp must defined in the session context<br> - the top level member "operation"(alias:"op") is defined in the json-rpc message object | "rpc.wotp"                                         | {<br>&nbsp;&nbsp;@context:"contextID",<br>&nbsp;&nbsp;method: "rpc.wotp",<br>&nbsp;&nbsp;op: "invokeAction"<br>&nbsp;&nbsp;...<br>} |

## [WoTP proposal 2 requirements](https://github.com/w3c/web-thing-protocol/issues/34#issue-2754869639)

### resolution: compliance with WoTP proposal 2 fields and operation names

- all massage fields, members, parameter, and property, names used in WoTP
  proposal 2 **must be** supported or aliased this rpc system extension.
- any tooling must output json objects using the same field an member names

<hr>

## (wip) top level @Context member setup

goal

- eliminate the need for sending a large string for the value of _\@context_
  after a connection session has been started.

## Messages

#### Table 1 common Wotp proposal 2 fields names

|    field name | proposed json-rpc system extension names |
| ------------: | :--------------------------------------- |
|          type | indicated by json-rpc message type spec  |
|     operation | method                                   |
|       thingID | thingID                                  |
|          name | affordanceID or affID                    |
| correlationID | correlationID or corrID                  |
|      senderID | senderID                                 |
|     messageID | id                                       |

### Request

#### Table 2

|            field name | proposed system extension name |
| --------------------: | :----------------------------- |
|     input (mandatory) | params                         |
| messageID (mandatory) | see Table 1                    |

#### Example 2

```
--> { 
        "jsonrpc": "2.0", 
        "method": "rpc.wotp",

        "@context": "ID assigned to the _negotiated_ schema and extension info",
        "affID": "string - Name of the affordance ", 
        "correlationID": "string - Unique identifier of the request...",
        "senderID": "string - Authenticated sender of the request.",
        "thingID": "string - ID of the thing the request",
 
        "id": "string - messageID", 
        "op": "<string - operation name>", 

        "params":  "any"
    }
```

### Notification

- full compliance with
  [json-rpc notification message](https://www.jsonrpc.org/specification#notification).

> 4.1 Notification<br> A Notification is a Request object without an "id"
> member.. Thus, there "MUST NOT" be A root/top-level member name "id".

#### Table 3

|           field name | proposed system extension names |
| -------------------: | :------------------------------ |
|     data (mandatory) | params                          |
| messageID (optional) | messageID or msgID              |

#### Example 3

```
--> { 
        "jsonrpc": "2.0", 
        "method": "rpc.wotp",

        "@context": "ID assigned to the _negotiated_ schema and extension info",
        "affID": "string - Name of the affordance ", 
        "correlationID": "string - Unique identifier of the request...",
        "senderID": "string - Authenticated sender of the request."
        "thingID": "string - ID of the thing the request",
  
        "msgID": "string - messageID", 
        "op":"<string - operation name>",

        "data":  "any"
    }
```

### Response (Success + Error) todo: ask @hspaay about this

#### Table 4

|          field name | proposed system extension names |
| ------------------: | :------------------------------ |
|    error (optional) | error                           |
|  output (mandatory) | result                          |
| received (optional) | rxTs                            |
|   status (optional) | status                          |
|  updated (optional) | udTs                            |

#### Example 3

```
<-- { 
        "jsonrpc": "2.0", 
        "method": "rpc.wotp",

        "@context": "ID assigned to the _negotiated_ schema and extension info",
        "affID": "string - Name of the affordance ", 
        "correlationID": "string - Unique identifier of the request...",
        "senderID": "string - Authenticated sender of the request.",
        "thingID": "string - ID of the thing the request",
 
        "id": "string - messageID", 
        "status": "string - messageID", 
        "op": "<string - operation name>", 

        "result":  "any"
    }
```
