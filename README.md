# ci
system: CI through Bazel, GO server, and GO client over secure communication

# Goal
* Automate CI
* Bazel based builds with build farm
* No Jenkins. Jenkins is a glorified set of threads shelling out to run jobs. This system will **instead use a GO gRPC/REST service**
* Integrate with GIT
* GO client to submit pull requests, see status etc
* TLS between client, server, Bazel, bazel-remote
* Optionally see logs, status, progress of CI through web-browser
* Reverse proxy so orthogonal PRs can be horizontally scaled

# Major Supported Use Cases
* Submit PR where proposed merge is done on build farm
* Local builds which also run Bazel for DEV work preceeding PR submission

# Use-case: Submit PR
* Code is committed to GIT
* PR requests merge between two repo/branches
* PR is validated 
* PR is approved
* PR is merged

```
   O
  -|-   ---+
  / \      |
 Alice     | Submit PR
  Dev      |
           |                       +-----------------------------------------------------------------------------+
   O       |   +------------+      |  +---------------+     +---------------+     +--------------+     +-------+ |     +--------+
  -|-   ---+-->| GO client  |<---->|  | reverse proxy |<--->| GO job server |<--->| Bazel remote |<--->| Bazel | |<--->| GITHUB |
  / \      |   +------------+      |  +---------------+     +---------------+     +--------------+     +-------+ |     +----+---+
  Bob      |                       +-----------------------------------------------------------------------------+          |
  Dev      |                                                                                                               / \ Approve/Reject PR
           |                                                                                                              /   \
   O       |                                                                                                            O      O
  -|-   ---+                                                                                                           -|-    -|-
  / \                                                                                                                  / \    / \
 Bubba                                                                                                                 Ramon  Judy
  Dev                                                                                                                  Lead   Lead
```

# Use-case: Local Build
* Dev edits code locally
* Dev runs Bazel to build
* Dev commits and pushes changes when done
* Dev submits PR; see above

In-progress: I am deciding if the DEV's code should exist on a local box `A` not in the build farm, and in which builds are done with normal Bazel build commands, or if the code is in the build farm somewhere and somehow the DEV has access to it by cross-mounts. In the later case builds would be done remotely.
