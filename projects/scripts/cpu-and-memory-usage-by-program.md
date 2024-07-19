---
title: Cpu And Memory Usage By Program
created: 2024-07-09
draft: false
publish: true
tags:
  - shell-utility
  - shell
---
# Cpu And Memory Usage By Program

You can change the `-k2` to `-k1` to sort by cpu usage

```bash
ps aux | awk '
BEGIN {
    printf "%-30s %-15s %-10s\n", "CPU(%)", "Memory(MB)", "Program";
}
NR > 1 {
    mem[$11] += $6;
    cpu[$11] += $3;
}
END {
    for (prog in mem) {
        printf "%-10.2f %-15.2f %-30s\n", cpu[prog], mem[prog]/1024, prog;
    }
}' | sort -k2 -n
```
