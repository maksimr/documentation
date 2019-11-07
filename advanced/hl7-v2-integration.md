---
description: >-
  This page describes HL7v2-IN module which provides API to ingest and process
  HL7 v2 messages.
---

# HL7 v2 Integration

## Introduction

In 2019 HL7 v2 is still most widely-used standard for healthcare IT systems integration. If you're developing a software which receives information from other systems within a hospital/clinic, most likely it will be HL7 v2 messages.

To process those messages, react on them and modify data stored in your Aidbox, there is a Hl7v2-in module. It provides two resources: `Hl7v2Config` and `Hl7v2Message`. `Hl7v2Config`determines how messages will be parsed and processed. `Hl7v2Message` represents a single received HL7 v2 message and contains raw representation, status \(processed/error\), error description in case of error and other useful information.

Both `Hl7v2Config` and `Hl7v2Message` are managed with standard CRUD API.

### Mapper module

Most likely when new HL7 v2 message is received, you want to make changes in the database - create, update or delete  FHIR resources. It's where the [Mapper module](mappings.md) comes on stage - HL7v2-IN module parses the message and then passes message data to the Mapping resource specified in `Hl7v2Config` instance via the `mapping` reference.

Here is an example of a mapping which creates a new Patient resource with name taken from `PID.5` field every time new message is received:

```yaml
resourceType: Mapping
id: test
body:
  resourceType: Bundle
  type: transaction
  entry:
    - resource:
        resourceType: Patient
        name:
          - given: ["$ msg.PID.name.0.given"]
            family: "$ msg.PID.name.0.family.surname"
      request:
        method: POST
        url: "/fhir/Patient"
```

{% hint style="info" %}
Please note that Mapping returns [FHIR Transaction Bundle](../basic-concepts/transaction.md), so it can produce as many CRUD operations as you need. Any other Aidbox operation/endpoint can be triggered as well \(for instance, [SQL endpoint](../basic-concepts/database.md)\).
{% endhint %}

### Creating a Hl7v2Config Resource

To start ingesting HL7 v2 messages, you need to create `Hl7v2Config` first:

```yaml
PUT /Hl7v2Config/default
Content-Type: text/yaml

resourceType: Hl7v2Config
id: default
isStrict: false
mapping:
  resourceType: Mapping
  id: test
```

#### Strict and Non-strict Parsing

`isStrict` attribute specifies if message parsing will be strict or not. When `isStrict` is true, HL7 v2 parser will fail on any schema error like missing required field or segment. If `isStrict` is false, then parser will try to produce AST of the message even when required fields are missing or segment hierarchy is broken. In this case you have a chance to get `null` values in your mapping for fields which you don't expect to be `null`.

#### Mapping Entrypoint

Refer to a mapping which will process your messages with `mapping` attribute. Please note that you don't obligated to keep all of your mapping logic inside a single `Mapping` resource - you can have one as an entrypoint and then dispatch execution to other mappings with `$include` directive.

### Submitting a Message

To submit a new HL7 v2 Message, just create it with a REST API:

```yaml
POST /Hl7v2Message
Content-Type: text/yaml

resourceType: Hl7v2Message
src: |-
  MSH|^~\&|AccMgr|1|||20151015200643||ADT^A01|599102|P|2.3|foo||
  EVN|A01|20151010045502|||||
  PID|1|010107111^^^MS4^PN^|1609220^^^MS4^MR^001|1609220^^^MS4^MR^001|BARRETT^JEAN^SANDY^^||19440823|F||C|STRAWBERRY AVE^FOUR OAKS LODGE^ALBUKERKA^CA^98765^USA^^||(111)222-3333||ENG|W|CHR|111155555550^^^MS4001^AN^001|123-22-1111||||OKLAHOMA|||||||N
  PV1|1|I|PREOP^101^1^1^^^S|3|||37^REID^TIMOTHY^Q^^^^^^AccMgr^^^^CI|||01||||1|||37^REID^TIMOTHY^Q^^^^^^AccMgr^^^^CI|2|40007716^^^AccMgr^VN|4|||||||||||||||||||1||G|||20050110045253||||||
  GT1|1|010107127^^^MS4^PN^|BARRETT^JEAN^S^^|BARRETT^LAWRENCE^E^^|2820 SYCAMORE AVE^TWELVE OAKS LODGE^MONTROSE^CA^91214^USA^|(818)111-3361||19301013|F||A|354-22-1840||||RETIRED|^^^^00000^|||||||20130711|||||0000007496|W||||||||Y|||CHR||||||||RETIRED||||||C
  IN1|1|0423|MEDICARE IP|^^^^     |||||||19951001|||MCR|BARRETT^JEAN^S^^|A|19301013|2820 SYCAMORE AVE^TWELVE OAKS LODGE^MONTROSE^CA^91214^USA^^^|||1||||||||||||||354221840A|||||||F|^^^^00000^|N||||010107127
  IN2||354221840|0000007496^RETIRED|||354221840A||||||||||||||||||||||||||||||Y|||CHR||||W|||RETIRED|||||||||||||||||(818)249-3361||||||||C
  IN1|2|0423|2304|AETNA PPO|PO BOX 14079^PO BOX 14079^LEXINGTON^KY^40512|||081140101400020|RETIRED|||20130101|||COM|BARRETT^JEAN^S^^|A|19301013|2820 SYCAMORE AVE^TWELVE OAKS LODGE^MONTROSE^CA^91214^USA^^^|||2||||||||||||||811001556|||||||F|^^^^00000^|N||||010107127
  IN2||354221840|0000007496^RETIRED|||||||||||||||||||||||||||||||||Y|||CHR||||W|||RETIRED|||||||||||||||||(818)249-3361||||||||C
status: received
config:
  resourceType: Hl7v2Config
  id: default
```

{% hint style="warning" %}
Newly created messages should have `received` status, otherwise they won't be processed.
{% endhint %}

`201 Created` response indicates that message was created and processed. Response body will contain additional processing details such as:

**status** - `processed` when message was successfully parsed and mapping was applied or `error` when there were an error/exception in one of those steps

**parsed** - structured representation of the message

**outcome** - Transaction Bundle returned by mapping or error information if status is `error`.

You can try to submit malformed message \(truncated\)  to see what's the result will be:

```yaml
POST /Hl7v2Message
Content-Type: text/yaml

resourceType: Hl7v2Message
src: |-
  MSH|^~
status: received
config:
  resourceType: Hl7v2Config
  id: default
```

### Using hl7proxy

Usually HL7 messages are transmitted using [MLLP protocol](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=55). To convert MLLP traffic to HTTP requests there is an open-source `hl7proxy` utility by Health Samurai. It's [available on GitHub](https://github.com/HealthSamurai/hl7proxy) and there are [pre-compiled binaries](https://github.com/HealthSamurai/hl7proxy/releases) for major operating systems and architectures.

Follow `hl7proxy`'s [README](https://github.com/HealthSamurai/hl7proxy) for installation and usage instructions.

### Using HAPI TestPanel to send test messages

Once `hl7proxy` is up and running, you can use [HAPI TestPanel](https://hapifhir.github.io/hapi-hl7v2/hapi-testpanel/) to send sample HL7 v2 messages. TestPanel shouldn't report any errors on message delivery. `hl7proxy` output should contain information about received message and `Sent to Aidbox: 201 Created` line which indicates successful delivery.
