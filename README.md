Download Link: https://assignmentchef.com/product/solved-database-project5
<br>
Concurrency ControlProject Hierarchy• Your project hierarchy should be like this.• Your_hconnect_repo• project5• include/• lib/• Makefile• src/• If your Makefile doesn’t make libbpt.a library file at the exact path, you’ll get zero score.(your_hconnect_repo/project5/lib/libbpt.a)Lock Manager• Your database system is not yet supporting transaction.• Implement transaction concept that can support ‘Isolation’ and ‘Consistency’ using your own lock manager (lock table).• Your lock manager should provide:• Conflict-serializable schedule for transactions• Strict-2PL• Deadlock detection (abort the transaction if detected)• Record-level locking with Shared(S)/Exclusive(X) mode

• Your library should provide two APIs below for transaction operations.• int trx_begin(void)• Allocate a transaction structure and initialize it.• Return a unique transaction id (&gt;= 1) if success, otherwise return 0.• Note that the transaction id should be unique for each transaction, that is, you need to allocate a transaction id holding a mutex.• int trx_commit(int trx_id)• Clean up the transaction with given trx_id (transaction id) and its related information that has been used in your lock manager. (Shrinking phase of strict 2PL)• Return the completed transaction id if success, otherwise return 0.• Also, your library should provide two APIs below for database operations, that can be wrapped in a transaction.• int db_find(int table_id, int64_t key, char* ret_val, int trx_id)• Read a value in the table with matching key for the transaction having trx_id.• return 0 (SUCCESS): operation is successfully done and the transaction can continue the next operation.• return non-zero (FAILED): operation is failed (e.g., deadlock detected) and the transaction should be aborted. Note that all tasks that need to be handled (e.g., releasing the locks that are held by this transaction, rollback of previous operations, etc… ) should be completed in db_find().• int db_update(int table_id, int64_t key, char* values, int trx_id)• Find the matching key and modify the values.• return 0 (SUCCESS): operation is successfully done and the transaction can continue the next operation.• return non-zero (FAILED): operation is failed (e.g., deadlock detected) and the transaction should be aborted. Note that all tasks that need to be handled (e.g., releasing the locks that are held on this transaction, rollback of previous operations, etc… ) should be completed in db_update().• Note that, in this project, you don’t have to support db_insert() or db_delete() working in a transaction that may require structure modifications on b+ tree.• We will first populate the database with db_insert(), or open a sample database file, and then run transactions in our test.

APIs for Lock Table Module• Your lock module’s APIs would like below. Use them appropriately in your database operation functions. ➣ It is accepted to change the APIs (return type, parameters, etc.) of the lock table module if you want.• int init_lock_table(void)• Initialize any data structures required for implementing lock table, such as hash table, lock table latch, etc.• If success, return 0. Otherwise, return non-zero value.• lock_t* lock_acquire(int table_id, int64_t key, int trx_id, int lock_mode)• Allocate and append a new lock object to the lock list of the record having the key.• If there is a predecessor’s conflicting lock object in the lock list, sleep until the predecessor to release its lock.• If there is no predecessor’s conflicting lock object, return the address of the new lock object.• If an error occurs, return NULL.• lock_mode: 0 (SHARED) or 1 (EXCLUSIVE)• int lock_release(lock_t* lock_obj)• Remove the lock_obj from the lock list.• If there is a successor’s lock waiting for the transaction releasing the lock, wake up the successor.• If success, return 0. Otherwise, return non-zero value.Hanyang UniversityTransaction Example

Lock Lock Lockacquired waiting waiting waiting

S Lock S Lock X Lock S Lock acquired acquired waiting waiting

db_find(1, 49)

Transaction 1

Lock Manager

Transaction Table

Transaction Abort1. 2. 3.100 AAAAUndo all modified records Release all acquired lock objects Remove the transaction table entry by the transaction100 AAAATrx 1 Trx 2 …

100 BBBBTransaction Table db_update(100, BBBB)abort• Your buffer manager must correctly work under concurrent accesses by multiple transactions.

Transaction 1. Acquire the buffer manager latchBuffer Manager Latch2. Acquire the page latch3. Release the buffer manager latchTransaction 1. Acquire the buffer manager latch

Transaction 1. Acquire the buffer manager latch

Page Latch &amp; Record Lock

Page Latch &amp; Record Lock• Page latch duration vs Record lock durationTransaction 1Wiki• Your wiki should contain descriptions about• lock mode (shared &amp; exclusive)• deadlock detection• abort and rollback• and whatever you want to describe.