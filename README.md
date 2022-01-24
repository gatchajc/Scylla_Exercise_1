Tasks to complete:

1. Create a 3 nodes cluster, with one datacenter, each of the nodes in a different rack , dc:north, racks north1, north2, north3.
2. Create a keyspace with 3 tables, one of the tables using STCS, another LCS, another TWCS.
3. Insert 10,000 records in each of the tables, using loop and cqlsh.
4. In the TWCS table, when creating the table and inserting data use time-window 30 minutes and the data to expire with 1 hour TTL.
5. Add a DC with 3 more nodes , each of the nodes in a different rack, dc: south, racks south1, south2, south3.
6. Install Scylla Manager.
7. Run repair using Scylla manager.
8. Decommission the old DC, keeping only the new created DC.
9. Add a node, decommission a node.
10. Then kill one of the nodes, destroy one of the containers (kill the seed node).
11. Replace procedure to replace this node we've killed.



*****


1. sudo docker exec -it Node_1 nodetool status
[sudo] password for janicagatchalian: 
Datacenter: datacenter1
=======================
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address     Load       Tokens       Owns    Host ID                               Rack
UN  172.17.0.3  586.17 KB  256          ?       eaa90066-9b62-47d3-af85-888974791bfc  rack1
UN  172.17.0.2  277.19 KB  256          ?       3335b216-3415-47db-9df7-d52e9c250bb2  rack1
UJ  172.17.0.4  ?          256          ?       758b5ea8-135e-442b-87da-cac9bc897469  rack1

Note: Non-system keyspaces don't have the same replication settings, effective ownership information is meaningless

2 & 4 [janicagatchalian@fedora ~]$ docker exec -it Node_1 cqlsh
Connected to  at 172.17.0.2:9042.
[cqlsh 5.0.1 | Cassandra 3.0.8 | CQL spec 3.3.1 | Native protocol v4]
Use HELP for help.
cqlsh> CREATE KEYSPACE mykeyspace WITH replication = {'class': 'NetworkTopologyStrategy', 'datacenter1' : 3};
cqlsh> user mykeyspace
   ... 
cqlsh> use mykeyspace ;
cqlsh:mykeyspace> CONSISTENCY QUORUM   
Consistency level set to QUORUM.
cqlsh:mykeyspace> CREATE TABLE STCS_table (firstname text, lastname text, age int, PRIMARY KEY (firstname)) WITH compaction = {'class': 'SizeTieredCompactionStrategy'};
cqlsh:mykeyspace> CREATE TABLE LCS_table (firstname text, lastname text, age int, PRIMARY KEY (firstname)) WITH compaction = {'class': 'LeveledCompactionStrategy'};
cqlsh:mykeyspace> CREATE TABLE TWCS_table (firstname text, lastname text, age int, PRIMARY KEY (firstname)) WITH compaction = {'class': 'TimeWindowCompactionStrategy', 'compaction_window_unit': 'MINUTES', 'compaction_window_size': 30} AND default_time_to_live = 3600;
cqlsh:mykeyspace> 

3 [janicagatchalian@fedora ~]$ sudo docker cp /home/janicagatchalian/Desktop/Scylla_Exercise_1.csv Node_1:/etc/

[sudo] password for janicagatchalian: 
[janicagatchalian@fedora ~]$ sudo docker exec -it Node_1 cqlsh
Connected to  at 172.17.0.2:9042.
[cqlsh 5.0.1 | Cassandra 3.0.8 | CQL spec 3.3.1 | Native protocol v4]
Use HELP for help.
cqlsh> 
cqlsh> use mykeyspace ;

cqlsh:mykeyspace> COPY lcs_table (firstname , lastname , age) FROM '/etc/Scylla_Exercise_1.csv' WITH DELIMITER = ';' AND HEADER = true ;
cqlsh:mykeyspace> COPY stcs_table (firstname , lastname , age) FROM '/etc/Scylla_Exercise_1.csv' WITH DELIMITER = ';' AND HEADER = true ;
cqlsh:mykeyspace> COPY twcs_table (firstname , lastname , age) FROM '/etc/Scylla_Exercise_1.csv' WITH DELIMITER = ';' AND HEADER = true ;
