<!-- F-series, Fs-series* -->

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  F-serien har ett lägre listpris per timme och har bästa prisprestanda i Azure-portföljen baserat på Azure-beräkningsenhet (ACU, Azure Compute Unit) per virtuell processor. 

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

## <a name="fs-series"></a>Fs-serien*

ACU: 210–250

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |2/750 |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2/1 500 |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4/3 000 |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8/6 000 |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8/6 000–12 000 &#8224; |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i Fs-serien kan begränsas av de anslutna diskarnas antal, storlek och striping.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/storage/storage-premium-storage.md).


<br>

## <a name="f-series"></a>F-serien

ACU: 210–250

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort/förväntade nätverksprestanda (Mbit/s) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_F1  | 1         | 2           | 16             | 3 000 / 46 / 23                                           | 2 / 2 x 500                         | 2/750                 |
| Standard_F2  | 2         | 4           | 32             | 6 000 / 93 / 46                                           | 4 / 4 x 500                         | 2/1 500                     |
| Standard_F4  | 4         | 8           | 64             | 12 000 / 187 / 93                                         | 8 / 8 x 500                         | 4/3 000                     |
| Standard_F8  | 8         | 16          | 128            | 24 000 / 375 / 187                                        | 16 / 16 x 500                       | 8/6 000                     |
| Standard_F16 | 16        | 32          | 256            | 48 000 / 750 / 375                                        | 32 / 32 x 500                       | 8/6 000–12 000 &#8224;           |


<br>


