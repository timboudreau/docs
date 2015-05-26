---
title: Exit Codes
---

These are the known exit codes returned by Syncthing:

<table>
<tr><th>Code</th><th>Meaning</th></tr>
<tr><td>0</td><td>Success / Shutdown</td></tr>
<tr><td>1</td><td>Error</td></tr>
<tr><td>2</td><td>Upgrade not available</td></tr>
<tr><td>3</td><td>Restarting</td></tr>
<tr><td>5</td><td>Upgrading</td></tr>
</table>

Some of these exit codes are only returned when running without a monitor process (with environment variable `STNORESTART` set).

Exit codes over 125 are usually returned by the shell/binary loader/default signal handler.

Exit codes over 128+N on Unix usually represent the signal which caused the process to exit. For example, `128 + 9 (SIGKILL) = 137`.
