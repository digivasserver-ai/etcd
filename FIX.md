# Fix Race Condition: Watch Misses Deletion Near Compaction Boundary

**Issue:** Concurrent watch() and compact() near compaction boundary could miss deletion event due to non-atomic check of compactMainRev.

**Fix:** Hold both watchableStore.Mu and store.Mu for entire revision check and watcher insertion, ensuring atomic decision between ErrCompacted vs synced/unsynced. Also ensure cancelCompactedWatchers and syncWatchers use same lock ordering (watchableStore.Mu -> store.Mu) to prevent deadlock and missed events.

**Verification:** TestConcurrentCompactionAndWatch now passes with deterministic ErrCompacted handling.
