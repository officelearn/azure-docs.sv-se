
### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

|   |  |
|---|:---:|
| Maximalt antal eDTU:er per pool | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Maximalt lagringsutrymme per pool (GB)*| &nbsp;&nbsp;&nbsp;&nbsp;10 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;20 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;39 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;73 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;117 |
| Maximalt antal databaser per pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Maximal InMemory-OLTP-lagring (GB) per pool| Saknas |
| Maximalt antal samtidiga arbetare per pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Maximalt antal samtidiga inloggningar per pool | &nbsp;&nbsp;&nbsp;200 &nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp;&nbsp;2400 |
| Maximalt antal samtidiga sessioner per pool | 4800 &nbsp;9600 &nbsp; 19200 &nbsp; 28800 &nbsp; 28800 |
| Maximalt antal eDTU:er per databas* | 5 |
| Minimalt antal eDTU:er per databas* | 0,5 |
| Maximalt lagringsutrymme per databas (GB)** | 2 |
| Återställning till tidpunkt | Någon tidpunkt de senaste 7 dagarna |
| Haveriberedskap | Aktiv geo-replikering |
|||

* Maximalt och minimalt antal eDTU:er per databas kan anges till något av de listade värdena så länge som den valda pool-DTU-storleken är minst lika stor som det maximala antalet eDTU:er per databas 

** Elastisk databas delar lagringsutrymme för pool, så databaslagring är begränsad till det mindre av återstående lagringsutrymme för pool eller maximalt lagringsutrymme per databas


### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

|   |  |
|---|:---:|
| Maximalt antal eDTU:er per pool | &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Maximalt lagringsutrymme per pool (GB)*| &nbsp;100 &nbsp;&nbsp;&nbsp; 200 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp;&nbsp;&nbsp; 1200 |
| Maximalt antal databaser per pool | &nbsp;200 &nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;400 |
| Maximal InMemory-OLTP-lagring (GB) per pool| Saknas |
| Maximalt antal samtidiga arbetare per pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Maximalt antal samtidiga inloggningar per pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Maximalt antal samtidiga sessioner per pool | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Maximalt antal eDTU:er per databas* | 10, 20, 50, 100 |
| Minimalt antal eDTU:er per databas* | 0, 10, 20, 50, 100 |
| Maximalt lagringsutrymme per databas (GB)** | 250 |
| Återställning till tidpunkt | Någon tidpunkt de senaste 35 dagarna |
| Haveriberedskap | Aktiv geo-replikering |
|||

* Maximalt och minimalt antal eDTU:er per databas kan anges till något av de listade värdena så länge som den valda pool-DTU-storleken är minst lika stor som det maximala antalet eDTU:er per databas 

** Elastisk databas delar lagringsutrymme för pool, så databaslagring är begränsad till det mindre av återstående lagringsutrymme för pool eller maximalt lagringsutrymme per databas

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

|   |  |
|---|:---:|
| Maximalt antal eDTU:er per pool | 125 &nbsp;&nbsp;&nbsp; 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 1000 &nbsp;&nbsp;&nbsp; &nbsp;1500 |
| Maximalt lagringsutrymme per pool (GB)*| 250 &nbsp;&nbsp;&nbsp; 500 &nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp; 750 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 750 |
| Maximalt antal databaser per pool | 50 |
| Maximal InMemory-OLTP-lagring (GB) per pool| &nbsp;&nbsp; 1 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 2 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 4 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; 8 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;12 |
| Maximalt antal samtidiga arbetare per pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Maximalt antal samtidiga inloggningar per pool | &nbsp;&nbsp;200 &nbsp;&nbsp;&nbsp; 400 &nbsp;&nbsp;&nbsp; 800 &nbsp;&nbsp; 1600 &nbsp;&nbsp;&nbsp; 2400 |
| Maximalt antal samtidiga sessioner per pool | 4800 &nbsp; 9600 &nbsp;19200 &nbsp;28800 &nbsp;&nbsp; 28800 |
| Maximalt antal eDTU:er per databas* | 125, 250, 500, 1000 |
| Minimalt antal eDTU:er per databas* | 0, 125, 250, 500, 1000 |
| Maximalt lagringsutrymme per databas (GB)** | 500 |
| Återställning till tidpunkt | Någon tidpunkt de senaste 35 dagarna |
| Haveriberedskap | Aktiv geo-replikering |
|||

* Maximalt och minimalt antal eDTU:er per databas kan anges till något av de listade värdena så länge som den valda pool-DTU-storleken är minst lika stor som det maximala antalet eDTU:er per databas 

** Elastisk databas delar lagringsutrymme för pool, så databaslagring är begränsad till det mindre av återstående lagringsutrymme för pool eller maximalt lagringsutrymme per databas


<!--HONumber=Nov16_HO2-->


