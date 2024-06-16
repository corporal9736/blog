---
title: 'MIT 6.824 Lab 2: Key-Value Server'
date: 2024-06-16
---

# design of cache
We have discussed what to cache in [note 1](./note1_linearizable#cache-linearizable-operations-and-idempotent), so this part is rather simple. We only need to cache the result of `Append` in this situation.  
After knowing what to cache, the next thing is about the data structure of cache. At first I used `map[int64]map[int]string`, where the outer key is the id of client, and the inner key is the index of request on that client. However, the `map` in golang will not free the memory of deleted items, so this cache will always use more memory than the tests expect. And according to the guide (2024):

> It's OK to assume that a client will make only one call into a Clerk at a time.

So I tried to use `map[int64]string` as cache, but it's too simple that I cannot distinguish between new requests and retried requests. Finally I use `map[int64]req` where `req` is a struct with the request index and cached result. In such design the replaced value can be recycled by the gc of golang. And one client only holds one cache, so not much memory is used.

# design of client
This part is rather simple given the assumption above. We don't need to notify the server about which cache can be dropped safely. However, if a client can send multiple requests concurrently, we will need a way to do that. We can maintain a list of failed request index, then we can always find the smallest index in the failed list. And the request before the smallest index are all succeed, thus can be safely removed from cache of server.


[return to index](./index)