---
title: RemoteIndexUpdated
---

Generated each time new index information is received from a device.

```json
{
    "id": 44,
    "type": "RemoteIndexUpdated",
    "time": "2014-07-13T21:04:35.394184435+02:00",
    "data": {
        "device": "NFGKEKE-7Z6RTH7-I3PRZXS-DEJF3UJ-FRWJBFO-VBBTDND-4SGNGVZ-QUQHJAG",
        "folder": "lightroom",
        "items": 1000
    }
}
```

<p class="message warning">
This event is intentionally light on the details about what changed in the index update, i.e. does not include the new global size or device completion percentage. These statistics are currently somewhat expensive to compute so it's better if an interested client asks for them when seeing this event, after proper debouncing.
</p>
