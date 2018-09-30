In Adaptive Replacement Cache
We have  4  LRU caches 

2 caches called as T1,T2 act as actual cache memory and store both keys and values in them .There combined maximum size is the size of the cache
T1 caches the recent entries.
T2 caches the frequent entries.

2 caches called as D1,D2 also known as Ghost Caches keep track of the recently evicted entries from T1 and T2 respectively.
They dont keep track of the values of the entries.

Whenever D1 is accessed for an entry the marker increases and size allocated to T1 is increased and T2 is decreased by 1 
Whenever D2 is accessed for an entry the marker decreases and size allocated to T2 is increased and T1 is decreased by 1

The Application Only support postgres db as it has the driver for only postgres assciated with it
User needs to provide DataBase conection details which are

DB url: The DataBase Connection URL 
user: The DataBase Server Username
password: The DataBase Server passsword

The Database should have a table by the name of dataset under public schema 

CREATE TABLE public.dataset
(
    key character varying(100) COLLATE pg_catalog."default" NOT NULL,
    value character varying(100) COLLATE pg_catalog."default" NOT NULL,
    CONSTRAINT "DataSet_pkey" PRIMARY KEY (key)
);

					############
					#Test Cases#
					############

Test Case 1
================================================================
--------------------------------------------------------------
DB Url:jdbc:postgresql://localhost:5432/TEST
DB Username:postgres
DB Password:234
Cache Size:4
No. Of Entries:7
Key:Key1
Value:A
Key:Key2
Value:B
Key:Key3
Value:C
Saving ->Key:Key1 ;Value: A
Key:Key4
Value:D
Saving ->Key:Key2 ;Value: B
Key:Key5 
Value:E
Saving ->Key:Key3 ;Value: C
Key:Key3
Value:F
Key:Key4
Value:G
Saving ->Key:Key3 ; Value: F
-------------------------------------------------------------

cache Size 4 

No. Of entries 7

Key : Key1 Value: A 
Explanation:Key,Value gets stored in T1

Key : Key2 Value: B  
Explanation:Key,Value gets stored in T1

Key : Key3 Value: C	 
Explanation:Key,Value gets stored in T1; 
Cache size crossed evicting entries from T1 ,
 saving "Key1" to DB , adding entry to D1
 
Key : Key4 Value: D  
Explanation:Key,Value gets stored in T1; 
Cache size crossed evicting entries from T1 , 
saving "Key2" to DB , adding entry to D1

Key : Key5 Value: E	 
Explanation:Key,Value gets stored in T1;
 Cache size crossed evicting entries from T1 ,
 saving "Key3" to DB , adding entry to D1 ,
 removing " Key 1" from D1
 
Key : Key3 Value: F  
Explanation:Key found in D1; marker increased (Max Size of T1 increased and T2 decreases) 
key removed from D1 and entry is added to T2
 
Key : Key4 Value: G  
Explanation:"Key4" found in T1;
key is moved to T2 .Cache size for T2 is crossed ,evicting entries from T2
Saving/Updating "Key3" to Db and adding key entry to D2


==========================================================================================




Test Case 2
==========================================================================================
------------------------------------------------------------------------------------------
DB Url:jdbc:postgresql://localhost:5432/TEST
DB Username:
postgres
DB Password:
1234
Cache Size:
6
No. Of Entries:
8
Key:
Key1
Value:
A
Key:Key2
Value:B
Key:Key2
Value:C
Key:Key3
Value:D
Key:Key4
Value:E
Key:Key3
Value:F
Key:Key4
Value:G
Key:Key1
Value:H
Saving ->Key:Key2 ; Value: C

------------------------------------------------------------------------------------------
cache Size 6

Key : Key1 Value: A 
Explanation:Key,Value gets stored in T1

Key : Key2 Value: B  
Explanation:Key,Value gets stored in T1

Key : Key2 Value: C	 
Explanation:Key,Value found in T1; moving Key2 to T2.
 
Key : Key3 Value: D  
Explanation:Key,Value gets stored in T1; 

Key : Key4 Value: E	 
Explanation:Value gets stored in T1;
 
Key : Key3 Value: F  
Explanation:"Key3" found in T1; 
moving "Key3" to T2.
 
Key : Key4 Value: G  
Explanation:"Key4" found in T1;
"Key4" is moved to T2 .

Key : Key1 Value: H  
Explanation:"Key1" found in T1;
"Key1" is moved to T2 . Cache Max size reached 
"Key 2" is evicted from T2 ,saved into DB and added to D2
=========================================================================================