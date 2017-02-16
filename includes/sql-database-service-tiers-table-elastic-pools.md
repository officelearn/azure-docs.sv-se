<!--
Used in:
sql-database-elastic-pool.md   
sql-database-resource-limits.md
sql-database-service-tiers.md  
-->

### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| Poolstorlek (eDTU:er)  | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Max storage per pool* | 5 GB | 10 GB | 20 GB | 29 GB | 39 GB | 78 GB | 117 GB | 156 GB |
| Maximal In-Memory OLTP-lagring per pool* | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas |
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga inloggningar per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Minimalt antal eDTU:er per databas | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} | {0, 5} |
| Maximalt antal eDTU:er per databas | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} | {5} |
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| Poolstorlek (eDTU:er)  | **50** | **100** | **200** | **300** | **400** | **800** | 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Max storage per pool* | 50 GB| 100 GB| 200 GB | 300 GB| 400 GB | 800 GB | 
| Maximal In-Memory OLTP-lagring per pool* | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | 
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Maximalt antal samtidiga inloggningar per pool | 100 | 200 | 400 | 600 |  800 | 1600 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Minimalt antal eDTU:er per databas | {0,10,20,<br>50} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Maximalt antal eDTU:er per databas | {10,20,<br>50} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.) 

| Poolstorlek (eDTU:er)  |  **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Max storage per pool* | 1,2 TB | 1,6 TB | 2 TB | 2,4 TB | 2,9 TB | 
| Maximal In-Memory OLTP-lagring per pool* | Saknas | Saknas | Saknas | Saknas | Saknas | 
| Maximalt antal databaser per pool | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga inloggningar per pool |  2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} | {0,10,20,<br>50,100} |
| Maximalt antal eDTU:er per databas | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | {10,20,<br>50,100} | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| Poolstorlek (eDTU:er)  | **125** | **250** | **500** | **1000** | **1500** | 
|:---|---:|---:|---:| ---: | ---: | 
| Max storage per pool* | 250 GB| 500 GB| 750 GB| 750 GB| 750 GB| 
| Maximal In-Memory OLTP-lagring per pool* | 1 GB| 2 GB| 4 GB| 10 GB| 12 GB| 
| Maximalt antal databaser per pool | 50 | 100 | 100 | 100 | 100 |  
| Maximalt antal samtidiga arbetare per pool | 200 | 400 | 800 | 1600 |  2400 | 
| Maximalt antal samtidiga inloggningar per pool | 200 | 400 | 800 | 1600 |  2400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | {0,25,50,75,<br>125} | {0,25,50,75,<br>125,250} | {0,25,50,75,<br>125,250,500} | {0,25,50,75,<br>125,250,500,<br>1000} | {0,25,50,75,<br>125,250,500,<br>1000,1500} | 
| Maximalt antal eDTU:er per databas | {25,50,75,<br>125} | {25,50,75,<br>125,250} | {25,50,75,<br>125,250,500} | {25,50,75,<br>125,250,500,<br>1000} | {25,50,75,<br>125,250,500,<br>1000,1500} |  
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.) 

| Poolstorlek (eDTU:er)  |  **2000** | **2500** | **3000** | **3500** | **4000** |
|:---|---:|---:|---:| ---: | ---: | 
| Max storage per pool* | 750 GB | 750 GB | 750 GB | 750 GB | 750 GB |
| Maximal In-Memory OLTP-lagring per pool* | 16 GB | 20 GB | 24 GB | 28 GB | 32 GB |
| Maximalt antal databaser per pool | 100 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga inloggningar per pool |  3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} | {0,25,50,75,<br>125,250,500,<br>1000,1750} |  {0,25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
| Maximalt antal eDTU:er per databas | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750} | {25,50,75,<br>125,250,500,<br>1000,1750,4000} | 
||||||||

\* pooldatabaser delar lagringsutrymme för pool, så databaslagring är begränsad till det mindre av återstående lagringsutrymme för pool eller maximalt lagringsutrymme per databas. Maximalt lagringsutrymme per pool avser det maximala lagringsutrymmet för datafiler i poolen och omfattar inte utrymme som används av loggfiler.



<!--HONumber=Jan17_HO3-->


