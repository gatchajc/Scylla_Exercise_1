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
