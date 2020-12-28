# digest-checker
Tool for checking if an image digest has multiple tags before deleting from a Docker Registry v2
---
Have you ever accidentally deleted an image that had multiple tags? Have you tried to cleanup space on a V2 Registry only to find out later that you have an image tag that is missing? `digest-checker` might be helpful for you.

### Use
```Bash
digest-checker {v2_registry}/{image_repository}:{image_tag}
```

### Example
```Bash
alpine
  ├── 20201218
  ├── 3.12.3
  ├── 3.12
  ├── 3
  ├── latest
  └── edge
hello-world
  ├── linux
  └── nanoserver-1809
ubuntu
  ├── 18.04
  ├── bionic-20201119
  ├── bionic
  ├── 20.04
  ├── focal-20201106
  ├── focal
  └── latest
```
Some of the above tags are shared tags (e.g. `ubuntu:20.04` and `ubuntu:focal`) but how would you know that? The Docker VS Code Extension (`ms-azuretools.vscode-docker` 1.9.0) allows a user to right click on a tag and select `Delete Image...`. If the tag that was selected for deletion is a shared tag, then the other tags would also be deleted.

If deleting `ububtu:20.04`, then the following tags would also be deleted: `focal`, `focal-20201106`, and `latest`. Use digest-checker to verify if a tag is a simply tag and is safe to delete, or if the underlying digest is represented by multiple tags (shared tag)

```
digest-checker {v2_registry}/ubuntu:20.04
...
MATCH: 20.04 = focal with digest: "sha256:..."
MATCH: 20.04 = focal-20201106 with digest: "sha256:..."
MATCH: 20.04 = latest with digest: "sha256:..."
...
WARNING: latest matched with 3 other tags. Please Manually validate before deleting the above tags!
```
