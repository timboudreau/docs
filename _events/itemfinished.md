---
title: ItemFinished
---

Generated when syncthing ends synchronizing a file to a newer version.

```json
{
    "id": 93,
    "type": "ItemFinished",
    "time": "2014-07-13T21:22:03.414609034+02:00",
    "data": {
        "item": "test.txt",
        "folder": "default",
        "error": null,
        "type": "file",
        "action": "update"
    }
}
```

<p class="message warning">
If error is not null, synchronizing the file has failed!
</p>
