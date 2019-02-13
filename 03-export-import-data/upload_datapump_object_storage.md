## Upload datapump file to Oracle object storage

**Prerequisite**

[Installing the CLI](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/cliinstall.htm)

**How to use CLI to upload datapump file to object storage**

```
oci os object bulk-upload -ns mynamespace -bn mybucket --src-dir path/to/upload/directory
```

More command options please see below link:
https://docs.cloud.oracle.com/iaas/tools/oci-cli/latest/oci_cli_docs/cmdref/os/object/bulk-upload.html
