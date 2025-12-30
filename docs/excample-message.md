# Example SVSD Message

This file contains a concrete SVSD message example and explains
**why this message exists**, **how it must be interpreted**, and
**why validation is mandatory**.

This is not a UI example.
This is not a generic event.
This is a **state-bound, schema-governed interaction contract**.

---

## Message Example

```json
{
  "context": {
    "requestId": "827fe939-82ce-4a16-ac7d-dc20924d989a",
    "employee": {
      "userId": "36dd8dbc-8afb-4a7b-8e1a-590782ca8cee",
      "login": "login"
    },
    "businessKey": "123",
    "status": "DRAFT",
    "payload": {
      "DRAFT": "process description"
    },
    "userTask": {
      "id": "c997a711-e3b5-11f0-bbd1-960a00d3e1e7",
      "name": "In Progress"
    }
  },
  "metaData": {
    "queueType": "type",
    "version": 1,
    "sourceSystem": "System UI",
    "schemaHash": "c21c21...82"
  },
  "schema": {
    "refineDraft": {
      "value": "",
      "type": "Json",
      "label": "Refine",
      "require": false
    }
  }
}
```

**What This Message Represents**

This message represents a single allowed interaction between:
- a Provider (process master / schema owner)
- and an Actor (consumer of the schema)

The Actor may be a UI or another service.
In all cases, the Actor is untrusted.

**status** - Process Capability
```
"status": "DRAFT"
```

```status``` **defines the current allowed process stage.**

It is:

- not a UI state
- not informational
- not optional

```status``` acts as a capability boundary:
only actions explicitly allowed for this status may occur.

If the status changes, the schema becomes invalid.

```schema``` - **Allowed Actions**

```json
"schema": {
"refineDraft": { ... }
}
```

Each **top-level key inside** ```schema``` is an allowed action.

- refineDraft is the action identifier
- its presence means the action is permitted
- absence means the action is forbidden

There is no implicit behavior.
Only declared actions exist.

**Action Content - Expected Event Contract**
```json
"refineDraft": {
"value": "",
"type": "Json",
"label": "Refine",
"require": false
}
```

This block describes the **expected event payload** for the action.

It defines:

- payload type
- structure expectations
- semantic meaning of the event

**The schema describes an expected event — not a UI form.**

UI rendering is only one possible interpretation.

**Why Validation Exists**

- Validation is mandatory because:
- the Actor is untrusted
- the process is stateful
- schema drift is forbidden

Validation guarantees that:

- the action is allowed for the current status
- the payload matches the expected event contract
- the schema was not modified by the Actor

**Validation Points**

1. **Gateway Validation**

- Before the Provider is called:
- schemaHash is verified
- action existence is checked
- payload is validated against the schema
- permission rules for the status are enforced

Invalid messages are rejected immediately.

2. **Provider Validation**

The Provider:

- validates business semantics
- validates domain rules
- decides whether the event is accepted

The Provider never infers intent.
It only processes declared actions.

**Key Takeaway**

- status defines where the process is
- chema keys define what may be done
- schema content defines what event is expected
- validation exists because trust does not

This message is the core interaction unit of SVSD.
