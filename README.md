# rust-mpbw
Thread-safe Multi Producer Buffered Writer for Rust

When writing to files, often users can write from a single thread in a sequential way.

Sometimes users don't want to write sequentially.
They just want to write without blocking.

This crate is designed for that purpose.
It does not guarantee ordering of writes, but the writes are done atomically.

This property is achieved by having a circular buffer of written data - Box<[u8]>.
The "write" blocks until it can write to the circular buffer and exits if it encounters pending data that hasn't been written.
If no data is pending, the writer takes on the role of an owner, and starts writing it's own write and any other writes it encounters to the enclosed buffered writer.
Once no more writes are found, the task ends.

It will be possible to invoke adding to the circular buffer without being a writer, though that does mean there is no leader.
It is up to the user to make sure data is flushed then.

Invoking flush will also write entries that haven't been written.
