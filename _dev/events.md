---
title: Event Interface
---

Syncthing provides a simple long polling interface for exposing events from the core utility towards a GUI.

To receive events, perform a HTTP GET of `/rest/events?since=<lastSeenID>`, where `<lastSeenID>` is the ID of the last event you've already seen or zero. Syncthing returns a JSON encoded array of event objects, starting at the event just after the one with the last seen ID. There is a limit to the number of events buffered, so if the rate of events is high or the time between polling calls is long some events might be missed. This can be detected by noting a discontinuity in the event IDs.

If no new events are produced since `<lastSeenID>`, the HTTP call blocks and waits for new events to happen before returning, or if no new events are produced within 60 seconds, times out.

To receive only a limited number of events, add the `limit=n` parameter with a suitable value for `n` and only the *last* `n` events will be returned. This can be used to catch up with the latest event ID after a disconnection for example: `/rest/events?since=0&limit=1`.

# Event Structure

Each event is represented by an object similar to the following:

```json
{
    "id": 2,
    "type": "DeviceConnected",
    "time": "2014-07-13T21:04:33.687836696+02:00",
    "data": {
        "addr": "172.16.32.25:22000",
        "id": "NFGKEKE-7Z6RTH7-I3PRZXS-DEJF3UJ-FRWJBFO-VBBTDND-4SGNGVZ-QUQHJAG"
    }
}
```

The top level keys `id`, `time`, `type` and `data` are always present, though `data` may be `null`.

* `id` is a monotonically increasing integer. The first event generated has id `1`, the next has id `2` etc.
* `time` is the time the event was generated.
* `type` indicates the type of (i.e. reason for) the event and is one of the event types below.
* `data` is an object containing optional extra information; the exact structure is determined by the event type.

# Event Types

## Starting

Emitted exactly once, when syncthing starts, before parsing configuration etc.

```json
{
    "id": 1,
    "type": "Starting",
    "time": "2014-07-17T13:13:32.044470055+02:00",
    "data": {
        "home": "/home/jb/.config/syncthing"
    }
}
```

## StartupComplete

Emitted exactly once, when initialization is complete and syncthing is ready to start exchanging data with other devices.

```json
{
    "id": 1,
    "type": "StartupComplete",
    "time": "2014-07-13T21:03:18.383239179+02:00",
    "data": null
}
```

## Ping

The Ping event is generated automatically every 60 seconds. This means that even in the absence of any other activity, the event polling HTTP request will return within a minute.

```json
{
    "id": 46,
    "type": "Ping",
    "time": "2014-07-13T21:13:18.502171586+02:00",
    "data": null
}
```

## DeviceDiscovered

Emitted when a new device is discovered using local discovery.

```json
{
    "id": 13,
    "type": "DeviceDiscovered",
    "time": "2014-07-17T13:28:05.043465207+02:00",
    "data": {
        "addrs": [
            "172.16.32.25:22000"
        ],
        "device": "NFGKEKE-7Z6RTH7-I3PRZXS-DEJF3UJ-FRWJBFO-VBBTDND-4SGNGVZ-QUQHJAG"
    }
}
```

## DeviceConnected

Generated each time a connection to a device has been established.

```json
{
    "id": 2,
    "type": "DeviceConnected",
    "time": "2014-07-13T21:04:33.687836696+02:00",
    "data": {
        "addr": "172.16.32.25:22000",
        "id": "NFGKEKE-7Z6RTH7-I3PRZXS-DEJF3UJ-FRWJBFO-VBBTDND-4SGNGVZ-QUQHJAG"
    }
}
```

## DeviceDisconnected

Generated each time a connection to a device has been terminated.

```json
{
    "id": 48,
    "type": "DeviceDisconnected",
    "time": "2014-07-13T21:18:52.859929215+02:00",
    "data": {
        "error": "unexpected EOF",
        "id": "NFGKEKE-7Z6RTH7-I3PRZXS-DEJF3UJ-FRWJBFO-VBBTDND-4SGNGVZ-QUQHJAG"
    }
}
```

> :exclamation: The `error` key contains the cause for disconnection, which might not necessarily be an *error* as such. Specifically, "EOF" and "unexpected EOF" both signify TCP connection termination, either due to the other device restarting or going offline or due to a network change.

## RemoteIndexUpdated

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

> :exclamation: This event is intentionally light on the details about what changed in the index update, i.e. does not include the new global size or device completion percentage. These statistics are currently somewhat expensive to compute so it's better if an interested client asks for them when seeing this event, after proper debouncing.

## LocalIndexUpdated

Generated when the local index information has changed, due to synchronizing one or more items from the cluster or discovering local changes during a scan.

```json
{
    "id": 59,
    "type": "LocalIndexUpdated",
    "time": "2014-07-17T13:27:28.051369434+02:00",
    "data": {
        "folder": "default",
        "items": 1000,
    }
}
```

## ItemStarted

Generated when syncthing begins synchronizing a file to a newer version.

```json
{
    "id": 93,
    "type": "ItemStarted",
    "time": "2014-07-13T21:22:03.414609034+02:00",
    "data": {
        "item": "test.txt",
        "folder": "default",
        "type": "file",
        "action": "update"
    }
}
```

## ItemFinished

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

> :exclamation: If error is not null, synchronizing the file has failed!

## StateChanged

Emitted when a folder changes state. Possible states are `idle`, `scanning`, `cleaning` and `syncing`. The field `duration` is the number of seconds the folder spent in state `from`. In the example below, the folder `default` was in state `scanning` for 0.198 seconds and is now in state `idle`.

```json
{
    "id": 8,
    "type": "StateChanged",
    "time": "2014-07-17T13:14:28.697493016+02:00",
    "data": {
        "folder": "default",
        "from": "scanning",
        "duration": 0.19782869900000002,
        "to": "idle"
    }
}
```

## FolderRejected

Emitted when a device sends index information for a folder we do not have, or have but do not share with the device in question.

```json
{
    "id": 27,
    "type": "FolderRejected",
    "time": "2014-08-19T10:41:06.761751399+02:00",
    "data": {
        "device": "EJHMPAQ-OGCVORE-ISB4IS3-SYYVJXF-TKJGLTU-66DIQPF-GJ5D2GX-GQ3OWQK",
        "folder": "unique"
    }
}
```

## DeviceRejected

Emitted when there is a connection from a device we are not configured to talk to.

```json
{
    "id": 24,
    "type": "DeviceRejected",
    "time": "2014-08-19T10:43:00.562821045+02:00",
    "data": {
        "address": "127.0.0.1:51807",
        "device": "EJHMPAQ-OGCVORE-ISB4IS3-SYYVJXF-TKJGLTU-66DIQPF-GJ5D2GX-GQ3OWQK"
    }
}
```

## ConfigSaved

Emitted after the config has been saved by the user or by Syncthing itself.

```json
{
    "id": 50,
    "type": "ConfigSaved",
    "time": "2014-12-13T00:09:13.5166486Z",
    "data":{
        "Version": 7,
        "Options": { ... },
        "GUI": { ... },
        "Devices": [ ... ],
        "Folders": [ ... ]
    }
}
```

## DownloadProgress

Emitted during file downloads for each folder for each file.
By default only a single file in a folder is handled at the same time, but custom configuration can cause multiple files to be shown.

```json
{
    "id": 221,
    "type": "DownloadProgress",
    "time": "2014-12-13T00:26:12.9876937Z",
    "data": {
        "folder1": {
            "file1": {
                "Total": 800,
                "Pulling": 2,
                "CopiedFromOrigin": 0,
                "Reused": 633,
                "CopiedFromElsewhere": 0,
                "Pulled": 38,
                "BytesTotal": 104792064,
                "BytesDone": 87883776
            },
            "dir\\file2": {
                "Total": 80,
                "Pulling": 2,
                "CopiedFromOrigin": 0,
                "Reused": 0,
                "CopiedFromElsewhere": 0,
                "Pulled": 32,
                "BytesTotal": 10420224,
                "BytesDone": 4128768
            }
        },
        "folder2": {
            "file3": {
                "Total": 800,
                "Pulling": 2,
                "CopiedFromOrigin": 0,
                "Reused": 633,
                "CopiedFromElsewhere": 0,
                "Pulled": 38,
                "BytesTotal": 104792064,
                "BytesDone": 87883776
            },
            "dir\\file4": {
                "Total": 80,
                "Pulling": 2,
                "CopiedFromOrigin": 0,
                "Reused": 0,
                "CopiedFromElsewhere": 0,
                "Pulled": 32,
                "BytesTotal": 10420224,
                "BytesDone": 4128768
            }
        }
    }
}
```

* `Total` - total number of blocks in the file
* `Pulling` - number of blocks currently being downloaded
* `CopiedFromOrigin` - number of blocks copied from the file we are about to replace
* `Reused` - number of blocks reused from a previous temporary file
* `CopiedFromElsewhere` - number of blocks copied from other files or potentially other folders
* `Pulled` - number of blocks actually downloaded so far
* `BytesTotal` - approximate total file size
* `BytesDone` - approximate number of bytes already handled (already reused, copied or pulled)

Where block size is 128KB.

Files/folders appearing in the event data imply that the download has been started for that file/folder,
where disappearing implies that the downloads has been finished or failed for that file/folder.
There is always a last event emitted with no data, which implies all downloads being finished/failed.

## FolderSummary

The FolderSummary event is emitted when folder contents have changed locally. This can be used to calculate the current local completion state.

```json
{
    "id": 16,
    "type": "FolderSummary",
    "time": "2015-04-17T14:12:20.460121585+09:00",
    "data": {
        "folder": "default",
        "summary": {
            "globalBytes": 0,
            "globalDeleted": 0,
            "globalFiles": 0,
            "ignorePatterns": false,
            "inSyncBytes": 0,
            "inSyncFiles": 0,
            "invalid": "",
            "localBytes": 0,
            "localDeleted": 0,
            "localFiles": 0,
            "needBytes": 0,
            "needFiles": 0,
            "state": "idle",
            "stateChanged": "2015-04-17T14:12:12.455224687+09:00",
            "version": 0
        }
    }
}
```

## FolderCompletion

The `FolderCompletion` event is emitted when the local or remote contents for a folder changes. It contains the completion percentage for a given remote device and is emitted once per currently connected remote device.

```json
{
    "id": 84,
    "type": "FolderCompletion",
    "time": "2015-04-17T14:14:27.043576583+09:00",
    "data": {
        "completion": 100,
        "device": "I6KAH76-66SLLLB-5PFXSOA-UFJCDZC-YAOMLEK-CP2GB32-BV5RQST-3PSROAU",
        "folder": "default"
    }
}
```
