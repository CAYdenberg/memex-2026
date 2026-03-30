
**Naming:**

Serenity(?)
Gorilla Radio(?)

- Should ask someone (jc333) about vite/wasm ... maybe there's  a better to do this in Tauri if we have direct access to a Rust runtime? Did he encounter the problem and solve by the same path?

## Sycning

Use https://automerge.org/docs/reference/repositories/networking/#messagechannel as a the baseline

See if we can get it working:
- with 2 peers replicating in one-direction
- with 2 peers replication in both directions
- with many peers that track one another... iroh gossip uses a 32 bytes identifier. Need to convert automerge url to that. (Maybe look at iroh-automerge)

Use iroh-gossip to connect multiple peers:
https://github.com/n0-computer/iroh-gossip

I think the "gossip topic" can be a 32 byte identifier generated from the automerge URL. The iroh-automerge https://github.com/n0-computer/iroh-examples/tree/main/iroh-automerge or https://github.com/n0-computer/iroh-examples/tree/main/iroh-automerge-repo may have code that does this already.

Other references:
https://gitlab.com/jcc333/automerge-repo-storage-tauri-fs/

Maybe should say Hello to this guy:
https://gitlab.com/jcc333