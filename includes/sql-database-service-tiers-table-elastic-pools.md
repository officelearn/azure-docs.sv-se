<!--
Used in:
sql-database-elastic-pool.md 
-->

 
### <a name="basic-elastic-pool-limits"></a>Gränser för grundläggande elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Ingår lagringsutrymme per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximalt antal lagringsalternativ per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Saknas | Saknas | Gäller inte |
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga inloggningar per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 |30000 | 30000 | 30000 | 30000 |
| Alternativ för min edtu: er per databas | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Alternativ för max edtu: er per databas | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Största lagringsutrymme per databas (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 
||||||||

### <a name="standard-elastic-pool-limits"></a>Standardgränser för elastisk pool

| eDTU:er per pool | **50** | **100** | **200** | **300** | **400** | **800**| 
|:---|---:|---:|---:| ---: | ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 | 
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 100 | 200 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximalt antal samtidiga inloggningar per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
| Alternativ för min edtu: er per databas | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Alternativ för max edtu: er per databas | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 | 
| Maximalt lagringsutrymme per databas (GB)* | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Standardgränser för elastisk pool (forts.) 

| eDTU:er per pool | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Ingår lagringsutrymme per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | Gäller inte | Saknas | Saknas | Saknas | Gäller inte | 
| Maximalt antal databaser per pool | 500 | 500 | 500 | 500 | 500 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga inloggningar per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för min edtu: er per databas | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Alternativ för max edtu: er per databas | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 | 
| Maximalt antal lagringsalternativ per databas (GB) * | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits"></a>Gränser för Premium elastisk pool

| eDTU:er per pool | **125** | **250** | **500** | **1000** | **1500**| 
|:---|---:|---:|---:| ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 250 | 500 | 750 | 1024 | 1536 | 
| Maximalt antal lagringsalternativ per pool (GB) * | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | 1 | 2 | 4 | 10 | 12 | 
| Maximalt antal databaser per pool | 50 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare per pool (begäranden) | 200 | 400 | 800 | 1600 | 2400 | 
| Maximalt antal samtidiga inloggningar per pool | 200 | 400 | 800 | 1600 | 2400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Minimalt antal eDTU:er per databas | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000, 1500 | 
| Maximalt antal eDTU:er per databas | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000, 1500 |
| Maximalt lagringsutrymme per databas (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

### <a name="premium-elastic-pool-limits-continued"></a>Premiumgränser för elastisk pool (forts.) 

| eDTU:er per pool | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: | 
| Ingår lagringsutrymme per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximalt antal lagringsalternativ per pool (GB) * | 2048 | 2560 | 3072 | 3548 | 4096|
| Max Minnesintern OLTP lagringsutrymme per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximalt antal databaser per pool | 100 | 100 | 100 | 100 | 100 | 
| Maximalt antal samtidiga arbetare (begäranden) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga inloggningar per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximalt antal samtidiga sessioner per pool | 30000 | 30000 | 30000 | 30000 | 30000 | 
| Alternativ för min edtu: er per databas | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Alternativ för max edtu: er per databas | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 | 
| Maximalt lagringsutrymme per databas (GB)* | 1024 | 1024 | 1024 | 1024 | 1024 | 
||||||||

> [!IMPORTANT]
> \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/). Lagringsstorlekar större än mängden lagringsutrymme som ingår finns i förhandsgranskningen och tillämpa extra kostnader. Mer information finns i [SQL-databas sida med priser](https://azure.microsoft.com/pricing/details/sql-database/).
>
> \* Premium-nivån mer än 1 TB lagringsutrymme är tillgängliga i följande regioner: Kanada Central, Kanada, Öst, Frankrike Central, Tyskland Central, östra, Korea Central, södra centrala USA, södra Östasien, USA East2, västra USA, oss Gov Virginia och Väst Europa. 
>

