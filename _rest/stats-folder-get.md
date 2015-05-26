---
loc: /rest/stats/folder
category: Statistics
method: GET
---

Returns general statistics about folders.
Currently, only contains the last synced file.

```bash
$ curl -s http://localhost:8384/rest/stats/folder | json
{
  "folderid" : {
    "lastFile" : {
      "filename" : "file/name",
        "at" : "2015-04-16T22:04:18.3066971+01:00"
      }
  }
}
```

