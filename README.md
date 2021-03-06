# TCache

TCache is a transactional cache written in Haskell with configurable persistence. It allows conventional STM transactions for objects that synchronize with a user-defined storage. State is kept synchronized between memory and permanent storage via transactions. Default persistence is via files, and is provided for testing purposes.

0.9.0.4 : Solves a bug in the management of weak pointers that evaporated registers from the cache
0.9.0.3 : Solves a lost registers bug.
0.9.0.1 : Solves a bug when object keys generate invalid filenames, and includes changes in defaultPersistence to further separate serialization from input-output.

0.9: Introduces full text search. Also adjusts serialization so it is achieved through byteStrings.

This version supports backward compatibility and also permits transparent retrieval of and transactions between objects without directly using STM references. It now provides explicit STM persistent references (`DBRef`s) that leverage clean, traditional haskell reference syntax for performing database transactions.

`DBRef`s are essentially persistent `TVar`s indexed in the cache using a traditional `readDBRef`/`writeDBRef` Haskell interface like that of the STM Monad.  Additionally, because `DBRef`s are serializable, they can be embedded in serializable registers. As they are references this means they point to other serializable registers. This enables persistent, mutable and efficient inter-object relations.

Triggers are also included in this release. These are user defined hooks that get called on register updates. They can be used for making the actualization of inter-object relations easier, and also permit higher-level customizable accesses. The query language internally uses triggers for its indexing.

This version also implements a straight-forward, non-intrusive, type-safe pure Haskell query language that is based on register field relations. This module can be imported separately. See `Data.TCache.IndexQuery` for further information.

The file persistence implementation is more reliable as `IO` reads are now in STM transactions.

To ease the implementation of other user-defined persistence, `Data.TCache.DefaultPersistence` needs to be imported explicitly for deriving file persistence instances.

The 0.9 version adds full-text indexing and search, which is incorporated into the experimental query language.

It also changes the default Persistence mechanism. Now `ByteString`s are used for serialization and deserialization. A `Serializable` class and a `Persist` structure decouples serialization from `ByteString` and read/write to files. Both can be redefined separately, so the default persistence could be changed with `setPersist` to write to blobs in a databases, for example. Default persistence now no longer has to be in files.
