## UDP repo

### Expectations
It is assumed that the UDP will store all Lecture Capture Caliper events, including those generated by users accessing the web application directly.

While Lecture Capture implements LTI and is accessible from the university's Canvas instance, it is also used as a standalone web application.  Users can access Lecture Capture directly without first authenticating against Canvas and initiating an LTI launch request.  This direct usage may also included unauthenticated users since some lectures are open to the public.  Events generated by these users are considered valuable.

### Envelope usage
Caliper `Event` and `Entity` data are transmitted inside an Envelope, a purpose-built JSON data structure that includes metadata about the emitting Sensor and the data payload. Each Event and Entity "describe" included in an envelope's data array MUST be expressed as a JSON-LD document.

The v1p1 `MediaEvent` examples include the envelope; the v1p0 examples do not.

#### Example: Caliper Envelope
```
{
  "sensor": "https://example.edu/sensors/1",
  "sendTime": "2017-11-15T10:15:01.000Z",
  "dataVersion":  "http://purl.imsglobal.org/ctx/caliper/v1p1",
  "data": [
      {
        "@context": "http://purl.imsglobal.org/ctx/caliper/v1p1",
        "id": "urn:uuid:71657137-8e6e-44f8-8499-e1c3df6810d2",
        "type": "MediaEvent",
        . . .
      }
  ]
}
```

### Event thinning
Caliper v1p1 `Event` and `Entity` representations are considerably "thinner" than their v1p0 predecssors.  Events that reference entities that share the same JSON-LD context should leverage JSON-LD inheritance rules and exclude duplicate `@context` references.  Optional `Event` and `Entity` properties with a value of null, empty or blank should also be omitted.

Use of JSON-LD `@id` and `@type` keywords as property names have also been deprecated in favor of `id` and `type` with aliases provided in the JSON-LD context.  Terms defined in the JSON-LD context (e.g., "MediaEvent", "Person", "Paused") can also be used as values in place of their IRIs.  These changes better reflect JSON-LD community practices.

#### Example: v1p0 MediaEvent
{
  "@context": "http://purl.imsglobal.org/ctx/caliper/v1/Context",
  "@type": "http://purl.imsglobal.org/caliper/v1/MediaEvent",
  "actor": {
    "@context": "http://purl.imsglobal.org/ctx/caliper/v1/Context",
    "@id": "https://leccap.engin.umich.edu/person/naufalza",
    "@type": "http://purl.imsglobal.org/caliper/v1/lis/Person",
    "name": null,
    "description": null,
    "dateCreated": null,
    "dateModified": null,
    "extensions": {}
  },
  "action": "http://purl.imsglobal.org/vocab/caliper/v1/action#Paused"
  . . .
}

#### Example: v1p1 MediaEvent
{
  "@context": "http://purl.imsglobal.org/ctx/caliper/v1p1",
  "id": "urn:uuid:3a648e68-f00d-4c08-aa59-8738e1884f2c",
  "type": "MediaEvent",
  "actor": {
    "id": "https://leccap.engin.umich.edu/person/naufalza",
    "type": "Person",
  },
  "action": "Paused"
  . . .
}

### MediaObject duration
The `duration` represents the length of the raw lecture but may not equate to what the user actually sees if the lecture has been edited. \[@lsloan -- "After I've thought about it more, I believe LC uses this value because an instructor may re-edit their lecture for another use at a later time. By keeping the same duration and internal timestamps, they can compare statistics of students' usage of the sections two editions of a lecture have in common."\]

### MediaEvent object and target properties
The v1p0 `MediaEvent` examples specify `MediaLocation` as the `object` of the interaction.  The v1p1 `MediaEvent` samples designates `VideoObject` as the `object` and references `MediaLocation` as the `target`.

#### Example: v1p0 MediaEvent object and target
```
{
  . . .
  "object": {
    "@context": "http://purl.imsglobal.org/ctx/caliper/v1/Context",
    "@id": "https://leccap.engin.umich.edu/leccap/viewer/r/woNYn8",
    "@type": "http://purl.imsglobal.org/caliper/v1/MediaLocation",
    . . .,
    "isPartOf": {
      "@id": "https://leccap.engin.umich.edu/leccap/viewer/r/woNYn8",
      "@context": "http://purl.imsglobal.org/ctx/caliper/v1/Context",
      "@type": "http://purl.imsglobal.org/caliper/v1/VideoObject",
      "name": "Lecture recorded on 9/19/2017",
      . . .
    },
    . . .
  },
  "target": null,
 . . .
}
```

#### Example: v1p1 MediaEvent object and target
```
{
  . . .
  "object": {
    "id": "https://leccap.engin.umich.edu/leccap/viewer/r/woNYn8",
    "type": "VideoObject",
    "name": "Lecture recorded on 9/19/2017",
    "dateCreated": "2017-10-01T16:08:02.000Z",
    "dateModified": "2017-10-01T21:28:59.000Z",
    "duration": "PT5099.000S",
    "isPartOf": {
      "id": "https://leccap.engin.umich.edu/leccap/site/ziiq5uzrd9k06e6vjew",
      "type": "WebPage",
      "name": "EECS 370 001 / 002 / 003 / 004",
      "dateCreated": "2017-10-01T06:41:17.000Z"
    }
  },
  "target": {
    "id": "https://leccap.engin.umich.edu/leccap/viewer/r/woNYn8",
    "type": "MediaLocation",
    "currentTime": "PT4540.137S",
    "isPartOf": {
      "id": "https://leccap.engin.umich.edu/leccap/viewer/r/woNYn8",
      "type": "VideoObject"
    }
  }
  . . .
}
```

### MediaEvent group and membership properties
TODO: explain why `group` and `membership` properties are not always provided.

### MediaEvent session property
TODO: explain why `session` properties are not always provided.

We plan to record session information whenever possible.

### OpenLRS source identifier
The `openlrsSourceId` property appended to each of the sample v1p0 events was provided by the openLRS data store in order to identify uniquely each `MediaEvent`.  For Caliper v1p1 each `Event` is provisioned with an `id` property.  The emitting application is responsible for assigning a UUID to each `Event` in the form of a URN using the format `urn:uuid:<UUID>`. 

### Appendix A. Property changes 
Property changes between v1p0 and v1p1 as reflected in the sample Media Profile events are noted in the tables below.  For a complete list of changes see the Caliper 1.1 specification, [Appendix H. Change Log] (https://github.com/IMSGlobal/caliper-spec/blob/master/caliper-spec.md#appendix-h-change-log).

#### Event properties
| Domain | Property | Status | Disposition |
| :------| :------- | :----: | :---------- |
| [Event](#event) | id | New | Each [Event](#event) MUST be provisioned with a [UUID](#uuidDef).  The UUID MUST be expressed as a [URN](#urnDef) using the form `urn:uuid:<UUID>` per [RFC 4122](#rfc4122).  A version 4 [UUID](#uuidDef) is RECOMMENDED. | 
| [Event](#event) | type | New | Replaces use of the [JSON-LD](#jsonldDef) `@type` keyword which is now aliased as `type` in the external IMS Caliper JSON-LD [context](http://purl.imsglobal.org/ctx/caliper/v1p1) document.  `type` string value also changed from [IRI](#iriDef) to [Term](#termDef), e.g. *MediaEvent*. |
| [Event](#event) | action | Revised | `action` string value changed from [IRI](#iriDef) to [Term](#termDef), e.g. *Paused*. |
| [Event](#event) | extensions | New | Adds the ability to include custom attributes not defined by the model. |

#### Entity properties
| Domain | Property | Status | Disposition |
| :------| :------- | :----: | :---------- |
| [Entity](#entity) | id | New | Replaces use of the [JSON-LD](#jsonldDef) keyword `@id` which is now aliased as `id` in the external IMS Caliper JSON-LD [context](http://purl.imsglobal.org/ctx/caliper/v1p1). |
| [Entity](#entity) | type | New | Replaces use of the [JSON-LD](#jsonldDef) `@type` keyword which is now aliased as `type` in the external IMS Caliper JSON-LD [context](http://purl.imsglobal.org/ctx/caliper/v1p1).  `type` string value also changed from [IRI](#iriDef) to [Term](#termDef), e.g. *Person*. |
| [Entity](#entity) | @type | Deprecated | Use `type`. |
| [DigitalResource](#digitalResource) | alignedLearningObjective | Deprecated | Targeted for removal in a future version of the specification.  Use `learningObjectives`. |
| [DigitalResource](#digitalResource) | creators | New | Adds the ability to specify the authors of the resource. |
| [DigitalResource](#digitalResource) | learningObjectives | New | Replaces the deprecated `alignedLearningObjective` property with a plural term that adheres to the naming format adopted for collections and lists. |
| [DigitalResource](#digitalResource) | mediaType | New | Adds the ability to specify the IANA media type that identifies the file format of the resource. |
| [DigitalResource](#digitalResource) | objectType | Deprecated | Targeted for removal in a future version of the specification.  Use `type`. |
| [MediaLocation](#mediaLocation) | currentTime | Revised | Datatype changed to an ISO 8601 formatted duration string set to UTC. | 
| [Membership](#membership) | roles | Revised | Individual role values changed from [IRI](#iriDef) to [Term](#termDef), e.g. *Learner*. |
| [Membership](#membership) | status | Revised | `status` string value changed from [IRI](#iriDef) to [Term](#termDef), e.g. *Active*. |
| [Session](#session) | user | New | Replaces deprecated `actor` property in order to provide a more concise term. |
| [SoftwareApplication](#softwareApplication) | version | New | Adds the ability to specify the current form or version of the [SoftwareApplication](#softwareApplication). |
