

Vissa arbetsbelastningar som SQL Server eller Oracle kräver RAM-minne, lagring, och i/o-bandbredd, men inte antalet hög kärnor. Många databasarbetsbelastningar är inte processorintensiva. Azure erbjuder vissa VM-storlekar där du kan begränsa antalet VM vCPU att minska kostnaden för programvarulicensiering samtidigt samma minne, lagring och i/o-bandbredd.

Antalet vCPU kan begränsas till hälften eller en kvartal ursprungliga VM-storlek. Dessa nya VM-storlekar ha ett suffix som anger antalet aktiva vCPUs så att de blir enklare att identifiera.

Till exempel den aktuella VM-storleken Standard_GS5 medföljer 32 vCPUs, 448 GB RAM-minne, 64 diskar (upp till 256 TB) och 80 000 IOPs eller 2 GBIT/s av i/o-bandbredd. Den nya VM-storlekar Standard_GS5 16 och Standard_GS5 8 medföljer 16 och 8 active vCPUs, samtidigt som resten av specifikationer för Standard_GS5 för minne, lagring och i/o-bandbredd.

Licensiering avgifter debiteras för SQL Server eller Oracle är begränsade till antalet vCPU och andra produkter bör debiteras baserat på antalet vCPU. Detta resulterar i en ökning förhållandet mellan VM-specifikationerna 50 till 75% till aktiv (fakturerbar) vCPUs. Dessa nya VM-storlekar som endast är tillgängliga i Azure, så att arbetsbelastningar till push högre CPU-användning på en bråkdel av licensiering (per kärna) kostnad. Beräkning kostnad, vilket innefattar OS-licensiering, förblir just nu är det samma som den ursprungliga storleken. Mer information finns i [Azure VM-storlekar för mer kostnadseffektiv databasarbetsbelastningar](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Namn                | Virtuell processor | Specifikationer           |
|---------------------|------|-----------------|
| Standard_M64 32ms   | 32   | Samma som M64ms   |
| 16 Standard_M64 MS   | 16   | Samma som M64ms   |
| Standard_M128 64ms  | 64   | Samma som M128ms  |
| Standard_M128 32ms  | 32   | Samma som M128ms  |
| Standard_E32 16_v3  | 16   | Samma som E32s_v3 |
| Standard_E32 8s_v3  | 8    | Samma som E32s_v3 |
| Standard_E64 32s_v3 | 32   | Samma som E64s_v3 |
| Standard_E64 16s_v3 | 16   | Samma som E64s_v3 |
| Standard_GS4-8      | 8    | Samma som GS4     |
| Standard_GS4 4      | 4    | Samma som GS4     |
| Standard_GS5 16     | 16   | Samma som GS5     |
| Standard_GS5-8      | 8    | Samma som GS5     |
| Standard_DS13 4_v2  | 4    | Samma som DS13_v2 |
| Standard_DS13 2_v2  | 2    | Samma som DS13_v2 |
| Standard_DS14 8_v2  | 8    | Samma som DS14_v2 |
| Standard_DS14 4_v2  | 4    | Samma som DS14_v2 |