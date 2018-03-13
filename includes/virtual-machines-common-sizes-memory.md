
Minnesoptimerade VM-storlekar erbjudande högt minne att CPU-förhållande som är bra för relationsdatabas servrar, medelstora till stora cacheminnen och analyser i minnet. Den här artikeln innehåller information om antalet vCPUs, diskar och nätverkskort samt lagring genomflöde och nätverket bandbredden för varje storlek i den här grupperingen. 

* M-serien ger högsta vCPU antal (upp till 128 vCPUs) och största minne (upp till 3,8 TiB) för en virtuell dator i molnet.  Serien är det perfekta valet för mycket stora databaser eller andra program som har nytta av många virtuella processorer och stora mängder minne.

* Dv2-serien, D-serien, G-serien och DS/GS-motsvarigheterna är idealiska för program som kräver snabbare virtuella processorer, bättre prestanda för temporär lagring eller som har högre minneskrav.  De utgör en kraftfull kombination för många program i företagsklass.

* Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till virtuell processor”-förhållande och en Solid State-hårddisk (SSD) för temporär lagring. Mer information finns i inlägget om [nya storlekar för virtuella datorer i D-serien](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) i Azure-bloggen.

* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den baseras på den senaste generationens 2,4 GHz Intel Xeon® E5-2673 v3-processor (Haswell) och kan uppnå 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.


## <a name="esv3-series-sup1sup"></a>Esv3-serien <sup>1</sup>

ACU: 160–190

Instanserna i ESv3-serien baseras på 2,3 GHz Intel XEON® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5 GHz med Intel Turbo Boost Technology 2.0 och använder premiumlagring. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.


| Storlek             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4 000/32 (50)                                                       | 3,200 / 48                                | 2 / 1,000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8 000/64 (100)                                                      | 6,400 / 96                                | 2 / 2,000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16 000/128 (200)                                                    | 12,800 / 192                              | 4 / 4,000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32 000/256 (400)                                                    | 25,600 / 384                              | 8 / 8,000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64 000/512 (800)                                                    | 51,200 / 768                              | 8 / 16,000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1 600)                                                   | 80 000/1 200                             | 8 / 30,000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128 000/1024 (1 600)                                                   | 80 000/1 200                             | 8 / 30,000                             |


<sup>1</sup> Esv3-serien VM funktion Intel® flertrådsteknik.

<sup>2</sup> begränsad core storlekar som finns tillgängliga.

<sup>3</sup> instans är isolerad till maskinvara som är dedikerad till en kund.


## <a name="ev3-series-sup1sup"></a>Ev3-serien <sup>1</sup>

ACU: 160–190 

Instanserna i Ev3-serien baseras på 2,3 GHz Intel XEON ® E5-2673 v4-processorn (Broadwell) och kan komma upp i 3,5GHz med Intel Turbo Boost Technology 2.0. Instanserna i Ev3-serien är idealiska för minnesintensiva företagsprogram.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du ESv3-storlekarna. Pris- och debiteringsmätarna för ESv3-storlekar är samma som för Ev3-serien. 


| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort/nätverksbandbredd |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3 000/46/23                                               | 2 / 1,000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6 000/93/46                                               | 2 / 2,000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12 000/187/93                                             | 4 / 4,000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24 000/375/187                                            | 8 / 8,000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48 000/750/375                                            | 8 / 16,000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30,000           |
| Standard_E64i_v3&nbsp;<sup>2</sup> | 64        | 432         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30,000           |

<sup>1</sup> Ev3-serien VM funktion Intel® flertrådsteknik.

<sup>2</sup> begränsad core storlekar som finns tillgängliga. 


## <a name="m-series-sup1sup"></a>M-serien <sup>1</sup>

ACU: 160–180

| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M64s  | 64   | 1024        | 2048           | 64             | 80 000/800 (6 348)       | 40,000 / 1,000                            | 8/16 000          |
| Standard_M64ms  | 64   | 1792        | 2048           | 64             | 80 000/800 (6 348)       | 40,000 / 1,000                            | 8/16 000          |
| Standard_M128s&nbsp;<sup>2,&nbsp;3</sup> | 128  | 2048        | 4096           | 64             | 160 000/1 600 (12 696) | 80,000 / 2,000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3800        | 4096           | 64             | 160 000/1 600 (12 696) | 80,000 / 2,000                            | 8 / 30000          |

<sup>1</sup> M-serien VM funktionen Intel® flertrådsteknik

<sup>2</sup> fler än 64 vCPU kräver en av dessa stöds gäst-OS: Windows Server 2016, Ubuntu 16.04 LTS, SLES 12 SP2 och Red Hat Enterprise Linux, CentOS 7.3 eller Oracle Linux 7.3 med LIS 4.2.1.

<sup>3</sup> begränsad core storlekar som finns tillgängliga.

<sup>4</sup> instans är isolerad till maskinvara som är dedikerad till en kund.
<br>

## <a name="gs-series-sup1sup"></a>GS-serien <sup>1</sup>

ACU: 180–240

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10,000 / 100 (264) |5,000 / 125 |2/2 000 |
| Standard_GS2 |4 |56 |112 |16 |20,000 / 200 (528) |10,000 / 250 |2/4 000 |
| Standard_GS3 |8 |112 |224 |32 |40,000 / 400 (1,056) |20,000 / 500 |4/8 000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80,000 / 800 (2,112) |40,000 / 1,000 |8/16 000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8/20 000 |

<sup>1</sup> maximal disk-genomströmning (IOPS eller Mbit/s) möjligt med GS-serien VM kan begränsas av hur många storlek och striping av de anslutna diskarna. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md). 

<sup>2</sup> instans är isolerad till maskinvara som är dedikerad till en kund.

<sup>3</sup> begränsad core storlekar som finns tillgängliga.

<br>

## <a name="g-series"></a>G-serien

ACU: 180–240

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6 000 / 93 / 46                                           | 8 / 8 x 500                       | 2/2 000                     |
| Standard_G2  | 4         | 56          | 768            | 12 000 / 187 / 93                                         | 16 / 16 x 500                       | 2/4 000                     |
| Standard_G3  | 8         | 112         | 1,536          | 24 000 / 375 / 187                                        | 32 / 32 x 500                     | 4/8 000                |
| Standard_G4  | 16        | 224         | 3,072          | 48 000 / 750 / 375                                        | 64 / 64 x 500                     | 8/16 000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6,144          | 96 000 / 1500 / 750                                       | 64 / 64 x 500                     | 8/20 000           |

<sup>1</sup> instans är isolerad till maskinvara som är dedikerad till en kund.
<br>


## <a name="dsv2-series-sup1sup"></a>DSv2-serien <sup>1</sup>

ACU: 210–250

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 96 |2/1 500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 192 |4/3 000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 384 |8/6 000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80,000 / 640 (720) |64,000 / 960 |8 / 25000&nbsp;<sup>4</sup>


<sup>1</sup> maximal disk-genomströmning (IOPS eller Mbit/s) möjligt med DSv2-serien VM kan begränsas av hur många storlek och striping av de anslutna diskarna.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md).

<sup>2</sup> instans är isolerad till maskinvara som är dedikerad till en kund.

<sup>3</sup> begränsad core storlekar som finns tillgängliga.

<sup>4</sup> 25000 Mbit/s med snabbare nätverk.

<br>

## <a name="dv2-series"></a>Dv2-serien

ACU: 210–250

| Storlek              | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1,000          | 60 000 / 937 / 468                                        | 64 / 64 x 500                       | 8 / 25000&nbsp;<sup>2</sup> |

<sup>1</sup> instans är isolerad till maskinvara som är dedikerad till en kund. 

<sup>2</sup> 25000 Mbit/s med snabbare nätverk.



<br>

## <a name="ds-series-sup1sup"></a>DS-serien <sup>1</sup>

ACU: 160

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8,000 / 64 (72) |6,400 / 64 |2/1 000 |
| Standard_DS12 |4 |28 |56 |16 |16,000 / 128 (144) |12,800 / 128 |4/2 000 |
| Standard_DS13 |8 |56 |112 |32 |32,000 / 256 (288) |25,600 / 256 |8/4 000 |
| Standard_DS14 |16 |112 |224 |64 |64,000 / 512 (576) |51,200 / 512 |8 / 8000 |

<sup>1</sup> maximal disk-genomströmning (IOPS eller Mbit/s) möjligt med DS-serien VM kan begränsas av hur många storlek och striping av de anslutna diskarna.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/virtual-machines/windows/premium-storage.md).



## <a name="d-series"></a>D-serien

ACU: 160

| Storlek         | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal datadiskar/dataflöde: IOPS | Maximalt antal nätverkskort / förväntat nätverksbandbredd (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6 000 / 93 / 46                                           | 8 / 8 x 500                         | 2/1 000                     |
| Standard_D12 | 4         | 28          | 200            | 12 000 / 187 / 93                                         | 16 / 16 x 500                         | 4/2 000                     |
| Standard_D13 | 8         | 56          | 400            | 24 000 / 375 / 187                                        | 32 / 32 x 500                       | 8/4 000                     |
| Standard_D14 | 16        | 112         | 800            | 48 000 / 750 / 375                                        | 64 / 64 x 500                       | 8 / 8000                |

<br>

