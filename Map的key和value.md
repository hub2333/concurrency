Stream，转Map过程中，不允许value为null，如出现则报NPE

HashTable/ConcurrentHashMap： key/value都不可以为null， 线程安全

TreeMap：key不可以为null，线程不安全

HashMap/Collections.synchronizedMap：都可以为null，synchronizedMap锁整张表