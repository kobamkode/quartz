---
id: scp-vs-rsync
aliases: []
tags:
  - linux
  - til
title: Scp vs Rsync
---

Today I learned that rsync is more efficient than scp.

I never had problems when using scp to copy data from remote to local, until today when I had to back up 3GB of data from a remote server because one of our servers was going to be reinstalled, and it took forever when using scp.

And the +1 of rsync for me is that it can ignore existing downloaded files.

Here's the explanation from Claude:

`rsync` is faster than `scp` for several reasons:

**1. Delta transfer algorithm (the big one)**
- `rsync` only transfers the *changed parts* of files, not entire files
- If you're updating a 1GB file with only 10MB changed, rsync transfers ~10MB while scp transfers the full 1GB
- For initial copies of new files, they're roughly the same speed

**2. Compression**
- `rsync` can compress data during transfer (use `-z` flag)
- Especially helpful on slower connections
- `scp` also has compression (`-C`), but rsync's is often more efficient

**3. Resumable transfers**
- `rsync` can resume interrupted transfers
- `scp` starts over from scratch if connection drops

**4. Batch processing**
- `rsync` is more efficient at handling many small files
- It can bundle operations more intelligently

**Real-world speed comparison:**
- **First-time copy**: Similar speeds (scp might even be slightly faster)
- **Subsequent syncs**: `rsync` is *dramatically* faster (only copies what changed)
- **Large file updates**: `rsync` wins by a huge margin

**Example:**
```bash
# First copy: both take ~10 minutes for 10GB
scp large_file user@host:/dest/
rsync large_file user@host:/dest/

# File gets 100MB update, second copy:
scp large_file user@host:/dest/        # ~10 minutes (full copy again)
rsync large_file user@host:/dest/      # ~10 seconds (only the delta)
```

For your use case with `--ignore-existing`, the speed difference on first copy will be minimal. The advantage is mainly in subsequent syncs or updates.
