---
title: 'MIT 6.824 Note 1: Linearizable'
date: 2024-06-15
---

# Operations
Here we use a straight forward definition of operation. We can consider one remote function call as one operation. Operation includes the action (referred by function name) and the result (referred by function return value). For example, in a kv(key-value) storage client, a call of `Put`, and its result, can be considered as an operation. I need to emphasize that the result is also part of the operation, which will be useful for later analysis.  
And in unstable situation, a remote function call might fail. We assume that the operation will be automatically retried, and we consider the duration of operation from the first function call to the final successful result.

# Linearizable point
Due to network latency and possible retries, one operation always takes some time to complete. So we can use a segment on a timeline to express this operation. We define the linearizable point as a point on the timeline as if the operation just happens on this single point.  
For example, we can observe from the server side in a kv storage if it has only one server. Although sending a remote function call through the internet takes a long time, the actual change of value inside the server's memory happens in a very short period of time, which can be treated as a point on the timeline. So we can pretend that the whole operation is accomplished atomically just in the time point. This point can be considered as linearizable point in real world.  
However, when we analyse if some operations are possible to be linearizable, we can assume that linearizable point can be **any** point within the operation's time segment. This means that for two operation overlaped on the timeline, we can try to switch the order of linearizable point and check if the order is legal.

# Linearizable
Linearizable is an attribute of a series of operations. For these operations, we can write down their linearizable points in order (the order is not unique, as we discussed above), and then check if the results of the operations are correct with the order. If we can find any arrangement that the results are right, these operations are linearizable.
![note1_1](./note1_1.svg)
For example, in the picture above, we have the same operation series and two orders of learizable points. The order (1) is correct because the value of `x` should be `"1"` if the lp of `Get` is earlier than the lp of `Append`, and order (2) is incorrect because in this order the result of `Get` should be `"1234"` instead of `"1"`.  
There is a legal order (order 1) of linearizable points, so this series of operations is linearizable.

# Cache, linearizable operations and idempotent
We often use cache on server side to achieve linearizable in a kv storage with single server. But we also need to think about what kind of operations should be cached while others may not. For example, we don't need to cache the `Get` operation. For the result between the latest value right before `Get` is called, and the lastest value before `Get` is returned, we can always arrange the linearizable point to make the series linearizable.
![note1_2](./note1_2.svg)
Here the result of `Get` can be "1", "2" or "3". We can absolutely linearize these results by arrange the linearizable points.  
Then we can try to explore what type of operation can be not cached. The answer **might** be idempotent operations, which means performing the operation multiple times will yield the same result. Here result refers not only to return value, but also to side effects on server side. So we can see that `Get` and `Put` can be not cached, but `Append` must be cached. However, this cannot garantee that every operation is produced at most once.


[return to index](./index.md)