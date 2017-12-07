<!-- F-series, Fs-series* -->

Beräkna optimerade VM-storlekar har förhållandet hög CPU-minne och är bra för medelhög trafik webbservrar, nätverksinstallationer, batchprocesser och programservrar. Den här artikeln innehåller information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen.

Fsv2-serien är baserad på Intel® Xeon® Platina 8168-processor med en frekvens som bas core 2.7 GHz och en största enkel kärna turbo frekvens 3.7 GHz. Intel® AVX-512 instruktioner som är nya Intel skalbara processorer, ger upp till en 2 X prestandaökning vector bearbetning arbetsbelastningarna på både enkla och dubbla precision flytande punkt åtgärder. De är med andra ord verkligen snabb för alla beräkningar arbetsbelastning. 

På en lägre per timme pris är Fsv2-serien det bästa värdet i pris prestanda i Azure portfölj baserat på den Azure Compute-enhet (ACU) per vCPU. 

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

## <a name="fsv2-series"></a>Fsv2-serien *

ACU: 195-210

| Storlek             | Vcpu's | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|------------------------------------------------|
| Standard_F2s_v2  | 2      | 4           | 16             | 4              | 4000 (32)                                                             | Mellan                                       |
| Standard_F4s_v2  | 4      | 8           | 32             | 8              | 8000 (64)                                                             | Mellan                                       |
| Standard_F8s_v2   | 8      | 16          | 64             | 16             | 16000 (128)                                                           | Hög                                           |
| Standard_F16s_v2 | 16     | 32          | 128            | 32             | 32000 (256)                                                           | Hög                                           |
| Standard_F32s_v2 | 32     | 64          | 256            | 32             | 64000 (512)                                                           | Mycket hög                                 |
| Standard_F64s_v2 | 64     | 128         | 512            | 32             | 128000 (1024)                                                         | Mycket hög                                 |
| Standard_F72s_v2 | 72     | 144         | 576            | 32             | 144000 (1520)                                                         | Mycket hög                                 |
* Fsv2-serien VM funktion Intel® flertrådsteknik

## <a name="fs-series"></a>Fs-serien*

ACU: 210–250

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |4 |4,000 / 32 (12) |3,200 / 48 |2/750 |
| Standard_F2s |2 |4 |8 |8 |8,000 / 64 (24) |6,400 / 96 |2/1 500 |
| Standard_F4s |4 |8 |16 |16 |16,000 / 128 (48) |12,800 / 192 |4/3 000 |
| Standard_F8s |8 |16 |32 |32 |32,000 / 256 (96) |25,600 / 384 |8/6 000 |
| Standard_F16s |16 |32 |64 |64 |64,000 / 512 (192) |51,200 / 768 |8/6 000–12 000 &#8224; |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i Fs-serien kan begränsas av de anslutna diskarnas antal, storlek och striping.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md).


<br>

## <a name="f-series"></a>F-serien

ACU: 210–250

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3 000 / 46 / 23                                           | 4 / 4 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8/6 000–12 000 &#8224;           |


<br>


