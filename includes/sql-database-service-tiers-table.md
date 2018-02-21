<!--
Used in:
sql-database-performance-guidance.md 
sql-database-single-database-resources.md 
-->

### <a name="basic-service-tier"></a>Grundläggande tjänstenivå
| **Prestandanivå** | **Basic** |
| :--- | --: |
| Maximala DTU:er | 5 |
| Ingår lagringsutrymme (GB) | 2 |
| Maximalt antal lagringsalternativ (GB) | 2 |
| Maximal InMemory-OLTP-lagring (GB) |Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden) | 30 |
| Maximalt antal samtidiga inloggningar | 30 |
| Maximalt antal samtidiga sessioner | 300 |
|||

### <a name="standard-service-tier"></a>Standardtjänstenivå
| **Prestandanivå** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|---:|
| Max dtu: er ** | 10 | 20 | 50 | 100 |
| Ingår lagringsutrymme (GB) | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) * | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Gäller inte | Gäller inte | Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden)| 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga inloggningar | 60 | 90 | 120 | 200 |
| Maximalt antal samtidiga sessioner |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-tjänstnivå (fortsättning)
| **Prestandanivå** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|---:|
| Max dtu: er ** | 200 | 400 | 800 | 1600 | 3000 |
| Ingår lagringsutrymme (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximalt antal lagringsalternativ (GB) * | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximal InMemory-OLTP-lagring (GB) | Gäller inte | Gäller inte | Gäller inte | Gäller inte |Gäller inte |
| Maximalt antal samtidiga arbetare (antal begäranden)| 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga inloggningar | 400 | 800 | 1600 | 3200 |6000 |
| Maximalt antal samtidiga sessioner |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium tjänstenivån 
| **Prestandanivå** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** | 
| :--- |---:|---:|---:|---:|---:|---:|
| Maximala DTU:er | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Ingår lagringsutrymme (GB) | 500 | 500 | 500 | 500 | 4096 | 4096 |
| Maximalt antal lagringsalternativ (GB) * | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096 | 4096 |
| Maximal InMemory-OLTP-lagring (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Maximalt antal samtidiga arbetare (antal begäranden)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga inloggningar | 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximalt antal samtidiga sessioner | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

> [!IMPORTANT]
> \*Lagringsstorlekar som är större än mängden lagringsutrymme som ingår finns i förhandsversionen, och extra kostnader tillkommer. Mer information finns i [Priser för SQL Database](https://azure.microsoft.com/pricing/details/sql-database/). 
>
>\* På Premium-nivån är mer än 1 TB lagringsutrymme för närvarande tillgängligt i följande regioner: Australien, östra, Australien, sydöstra, Brasilien, södra, Kanada, centrala, Kanada, östra, USA, centrala, Frankrike, centrala, Tyskland, centrala, Japan, östra, Japan, västra, Korea, centrala, USA, norra centrala, Nordeuropa, USA, södra centrala, Sydostasien, Storbritannien, södra, Storbritannien, västra, USA, östra 2, USA, västra, Virginia (USA-förvaltad region) och Europa, västra. Se [sidan 11-15 i Aktuella begränsningar](../articles/sql-database/sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> 
>\*\* Max Dtu per databas startar 200 dtu: er och högre i Standard finns i förhandsgranskningen.
>
