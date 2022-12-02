1) I saw someone's code fail to compile because they 
were trying to send non-thread-safe data across threads. 
How does the Rust language allow for static (i.e. at compile time)
guarantees that specific data can be sent/shared acrosss threads?

By introducing the guarantees into the type system, the compiler can statically check if 
each data is safe for sharing across threads. This is via the send and sync marker traits, 
that specify respectively if it is safe to share the data, or share a reference of the data
across threads. There are no methods specified for these traits, due to being MARKER traits,
but they just act as identifiers. 

2) Do you have to then implement the Send and Sync traits for 
every piece of data (i.e. a struct) you want to share and send across threads?

No, these traits are inferred for data, depending on their contents. If all
members of a struct are Send/Sync, then the overarching struct is also Send/Sync

3) What types in the course have I seen that aren't Send? Give one example, 
and explain why that type isn't Send 

Rc is not send, because it is unstable to let two threads update a
reference counter at once. its alternate atomic version Arc, allows for the
safe reference counter update to occur atomically. 

4) What is the relationship between Send and Sync? Does this relate
to Rust's Ownership system somehow?

Sync is often defined in terms of Send, such that a type is only Sync, if its reference is Send. 
Similar to having a mutual reference, a sync type cannot be dropped, or modified, only accessed.
alternatively, Send is more similar to an exclusive reference, that can be dropped, modified.

5) Are there any types that could be Send but NOT Sync? Is that even possible?

Cell, RefCell, as these don't give out references to the inner types. Resultingly,
a mutable reference must always be held , and hence only Send NOT Sync

6) Could we implement Send ourselves using safe rust? why/why not?

No, because it would mean we have to reason about the memory safety of a type
to the extent that the rust compiler cannot verify our logic. if it is
not able to be verified by the compiler, it is unsafe.
