


Du kan välja mellan flera standardstorlekar i Azure. Det finns några saker som du bör tänka på när du väljer storlek:

* Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till kärna”-förhållande och en Solid State-hårddisk (SSD) för den tillfälliga disken. Mer information finns i inlägget om [nya storlekar för virtuella datorer i D-serien](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) i Azure-bloggen.
* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den baseras på den senaste generationens 2,4 GHz Intel Xeon® E5-2673 v3-processor (Haswell) och kan uppnå 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.
* F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  F-serien har ett lägre listpris per timme och har bästa prisprestanda i Azure-portföljen baserat på Azure-beräkningsenhet (ACU, Azure Compute Unit) per kärna. 
  
    F-serien inför dessutom en ny namngivningsstandard för VM-storlekar i Azure. För den här serien och för framtida VM-storlekar motsvarar det numeriska värdet efter produktfamiljens bokstav antalet processorkärnor. Ytterligare funktioner och egenskaper, t.ex. ”optimerad för Premium Storage”, representeras av bokstäver efter det numeriska värdet för antal processorkärnor. Det här namngivningsformatet kommer att användas för framtida VM-storlekar. Namnen på befintliga VM-storlekar som redan har publicerats påverkas dock inte.
* Virtuella datorer i G-serien erbjuder mest minne och körs på värdar som är utrustade med processorer i Intel Xeon E5 V3-familjen.
* Virtuella datorer i DS-serien, DSv2-serien, Fs-serien och GS-serien kan använda Premium Storage, som tillhandahåller lagring med låg fördröjning och höga prestanda för I/O-intensiva arbetsbelastningar. Dessa virtuella datorer använder solid state-hårddiskar (SSD) som värdar för diskarna på en virtuell dator och tillhandahåller också en lokal SSD-diskcache. Premium Storage är tillgängligt i vissa regioner. Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/storage/storage-premium-storage.md).
*   Virtuella datorer i A-serien och Av2-serien kan distribueras på en rad olika maskinvarutyper och processorer. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.
* A0-storleken har för hög andel prenumerationer på den fysiska maskinvaran. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

Storleken på den virtuella datorn påverkar priset. Storleken påverkar också den virtuella datorns bearbetnings-, minnes- och lagringskapacitet. Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information finns i avsnitten med [prisinformation om virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/) och [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/). 

Följande information kan hjälpa dig att välja storlek:

* Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz. Detaljerad information och saker att tänka på när du använder dessa storlekar finns i avsnittet [om H-serien och beräkningsintensiva virtuella datorer i A-serien](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Dv2-serien, D-serien, G-serien och DS/GS-motsvarigheterna är idealiska för program som kräver snabbare processorer, bättre lokala diskprestanda eller som har högre minneskrav.  De utgör en kraftfull kombination för många program i företagsklass.
* De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller lokal SSD per processorkärna.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.
* Vissa av de fysiska värdarna i Azure-datacenter kanske inte stöder större storlekar för virtuell dator, till exempel A5–A11. Därför kan det hända att felmeddelandet **Det gick inte att konfigurera den virtuella datorn<machine name>** eller **Det gick inte att skapa den virtuella datorn<machine name>** visas om du ändrar storlek på en befintlig virtuell dator, om du skapar en ny virtuell dator i ett virtuellt nätverk som skapades före den 16 april 2013 eller om du lägger till en ny virtuell dator i en befintlig molntjänst. Se [Error: “Failed to configure virtual machine”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) (Fel: "Det gick inte att konfigurera den virtuella datorn”) i supportforumet för lösningar för respektive distributionsscenario.  
* Din prenumeration kan även begränsa hur många kärnor som du kan distribuera i vissa storleksfamiljer. Kontakta Azure-supporten om du vill öka en kvot.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
Vi har skapat konceptet Azure-beräkningsenheter (ACU, Azure Compute Unit) för att göra det lättare att jämföra beräkningsprestanda (CPU) mellan Azure SKU:er. På så sätt blir det lättare att identifiera vilken SKU som bäst uppfyller dina behov.  ACU är för närvarande standardiserat på en liten virtuell dator (Standard_A1) och är 100, och alla andra SKU:er representerar ungefär hur mycket snabbare den SKU:n kan köra ett benchmark-standardtest. 

> [!IMPORTANT]
> ACU är endast en riktlinje.  Resultatet med din arbetsbelastning kan variera. 
> 
> 

<br>

| SKU-familj | ACU/kärna |
| --- | --- |
| [A0](#a-series) |50 |
| [A1-A4](#a-series) |100 |
| [A5-A7](#a-series) |100 |
| [A1_v2-A8_v2](#av2-series) |100 |
| [A2m_v2-A8m_v2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-D14](#d-series) |160 |
| [D1_v2-D15_v2](#dv2-series) |210 - 250* |
| [DS1-DS14](#ds-series) |160 |
| [DS1_v2-DS15_v2](#dsv2-series) |210-250* |
| [F1-F16](#f-series) |210-250* |
| [F1s-F16s](#fs-series) |210-250* |
| [G1-G5](#g-series) |180 - 240* |
| [GS1-GS5](#gs-series) |180 - 240* |
| [H](#h-series) |290 - 300* |
| [L4s-L32s](#l-series) |180 - 240* |

ACU:er som visas med * använder Intel® Turbo-teknik för att öka processorfrekvensen och prestanda.  Prestandaökningens storlek kan variera beroende på storleken på den virtuella datorn, arbetsbelastningen och andra arbetsbelastningar som körs på samma värd.

## <a name="size-tables"></a>Storlekstabeller
Följande tabeller visar storlekarna och den kapacitet som de tillhandahåller.

* Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
* Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
* Datadiskar kan köras i cachelagrat eller icke cachelagrat läge.  För diskåtgärder med cachelagrade data anges cacheläget till **ReadOnly** eller **ReadWrite**.  För diskåtgärder med icke cachelagrade data anges cacheläget till **Inget**.
* Den maximala nätverksbandbredden är den högsta aggregerade bandbredden som allokeras och tilldelas per typ av virtuell dator. Den maximala bandbredden är en riktlinje som hjälper dig att välja rätt typ av virtuell dator för att säkerställa tillräcklig nätverkskapacitet. Genomflödet ökar när du växlar mellan Låg, Måttlig, Hög och Mycket hög. Faktiska nätverksprestanda beror på många faktorer, bland annat nätverks- och programbelastningar och programmets nätverksinställningar.

## <a name="a-series"></a>A-serien
| Storlek | Processorkärnor | Minne: GiB | Lokal hårddisk: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0.768 |20 |1 |1 × 500 |1 / låg |
| Standard_A1 |1 |1.75 |70 |2 |2 × 500 |1 / måttlig |
| Standard_A2 |2 |3.5 |135 |4 |4 × 500 |1 / måttlig |
| Standard_A3 |4 |7 |285 |8 |8 × 500 |2 / hög |
| Standard_A4 |8 |14 |605 |16 |16 × 500 |4 / hög |
| Standard_A5 |2 |14 |135 |4 |4 × 500 |1 / måttlig |
| Standard_A6 |4 |28 |285 |8 |8 × 500 |2 / hög |
| Standard_A7 |8 |56 |605 |16 |16 × 500 |4 / hög |

<br>

## <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser
Mer information och saker att tänka på när du använder dessa storlekar finns i avsnittet [om H-serien och beräkningsintensiva virtuella datorer i A-serien](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Storlek | Processorkärnor | Minne: GiB | Lokal hårddisk: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 × 500 |2 / hög |
| Standard_A9* |16 |112 |382 |16 |16 × 500 |4 / mycket hög |
| Standard_A10 |8 |56 |382 |16 |16 × 500 |2 / hög |
| Standard_A11 |16 |112 |382 |16 |16 × 500 |4 / mycket hög |

*RDMA-stöd

<br>

## <a name="av2-series"></a>Av2-serien

| Storlek        | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
|-------------|-----------|--------------|-----------------------|----------------|--------------------|-----------------------|
| Standard_A1_v2 | 1         | 2            | 10                   | 2              | 2 × 500              | 1 / måttlig              |
| Standard_A2_v2 | 2         | 4            | 20                   | 4              | 4 × 500              | 2 / måttlig              |
| Standard_A4_v2 | 4         | 8            | 40                   | 8              | 8 × 500              | 4 / hög                  |
| Standard_A8_v2 | 8         | 16           | 80                   | 16             | 16 × 500             | 8 / hög                  |
| Standard_A2m_v2 | 2        | 16           | 20                   | 4              | 4 × 500              | 2 / måttlig              |
| Standard_A4m_v2 | 4        | 32           | 40                   | 8              | 8 × 500              | 4 / hög                  |
| Standard_A8m_v2 | 8        | 64           | 80                   | 16             | 16 × 500             | 8 / hög                  |


## <a name="d-series"></a>D-serien
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3.5 |50 |2 |2 × 500 |1 / måttlig |
| Standard_D2 |2 |7 |100 |4 |4 × 500 |2 / hög |
| Standard_D3 |4 |14 |200 |8 |8 × 500 |4 / hög |
| Standard_D4 |8 |28 |400 |16 |16 × 500 |8 / hög |
| Standard_D11 |2 |14 |100 |4 |4 × 500 |2 / hög |
| Standard_D12 |4 |28 |200 |8 |8 × 500 |4 / hög |
| Standard_D13 |8 |56 |400 |16 |16 × 500 |8 / hög |
| Standard_D14 |16 |112 |800 |32 |32 ×&500; |8 / mycket hög |

<br>

## <a name="dv2-series"></a>Dv2-serien
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3.5 |50 |2 |2 × 500 |1 / måttlig |
| Standard_D2_v2 |2 |7 |100 |4 |4 × 500 |2 / hög |
| Standard_D3_v2 |4 |14 |200 |8 |8 × 500 |4 / hög |
| Standard_D4_v2 |8 |28 |400 |16 |16 × 500 |8 / hög |
| Standard_D5_v2 |16 |56 |800 |32 |32 ×&500; |8 / extremt hög |
| Standard_D11_v2 |2 |14 |100 |4 |4 × 500 |2 / hög |
| Standard_D12_v2 |4 |28 |200 |8 |8 × 500 |4 / hög |
| Standard_D13_v2 |8 |56 |400 |16 |16 × 500 |8 / hög |
| Standard_D14_v2 |16 |112 |800 |32 |32 ×&500; |8 / extremt hög |
| Standard_D15_v2** |20 |140 |1,000 |40 |40 × 500 |8 / extremt hög* |

* Snabbare nätverksfunktioner är tillgängliga för Standard_D15_v2-storleken i vissa regioner. Mer information om användning och tillgänglighet finns i [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Snabbare nätverksfunktioner finns som förhandsversion) och [Accelerated Networking for a virtual machine](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md) (Snabbare nätverksfunktioner för en virtuell dator).

**Instansen är isolerad till maskinvara som är dedikerad till en enda kund.

<br>

## <a name="ds-series"></a>DS-serien*
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt cachelagrat diskgenomflöde: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 32 |1 / måttlig |
| Standard_DS2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 64 |2 / hög |
| Standard_DS3 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 128 |4 / hög |
| Standard_DS4 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 256 |8 / hög |
| Standard_DS11 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 64 |2 / hög |
| Standard_DS12 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 128 |4 / hög |
| Standard_DS13 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 256 |8 / hög |
| Standard_DS14 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 512 |8 / mycket hög |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i DS-serien kan begränsas av de anslutna diskarnas antal, storlek och striping.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="dsv2-series"></a>DSv2-serien*
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt cachelagrat diskgenomflöde: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |2 |4,000 / 32 (43) |3,200 / 48 |1 måttlig |
| Standard_DS2_v2 |2 |7 |14 |4 |8,000 / 64 (86) |6,400 / 96 |2 hög |
| Standard_DS3_v2 |4 |14 |28 |8 |16,000 / 128 (172) |12,800 / 192 |4 hög |
| Standard_DS4_v2 |8 |28 |56 |16 |32,000 / 256 (344) |25,600 / 384 |8 hög |
| Standard_DS5_v2 |16 |56 |112 |32 |64,000 / 512 (688) |51,200 / 768 |8 extremt hög |
| Standard_DS11_v2 |2 |14 |28 |4 |8,000 / 64 (72) |6,400 / 96 |2 hög |
| Standard_DS12_v2 |4 |28 |56 |8 |16,000 / 128 (144) |12,800 / 192 |4 hög |
| Standard_DS13_v2 |8 |56 |112 |16 |32,000 / 256 (288) |25,600 / 384 |8 hög |
| Standard_DS14_v2 |16 |112 |224 |32 |64,000 / 512 (576) |51,200 / 768 |8 extremt hög |
| Standard_DS15_v2*** |20 |140 |280 |40 |80,000 / 640 (720) |64,000 / 960 |8 extremt hög** |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i DSv2-serien kan begränsas av de anslutna diskarnas antal, storlek och striping.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/storage/storage-premium-storage.md).

** Snabbare nätverksfunktioner är tillgängliga för Standard_DS15_v2-storleken i vissa regioner. Mer information om användning och tillgänglighet finns i [Accelerated Networking is in Preview](https://azure.microsoft.com/updates/accelerated-networking-in-preview/) (Snabbare nätverksfunktioner finns som förhandsversion) och [Accelerated Networking for a virtual machine](../articles/virtual-network/virtual-network-accelerated-networking-powershell.md) (Snabbare nätverksfunktioner för en virtuell dator).

***Instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>

## <a name="f-series"></a>F-serien
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_F1 |1 |2 |16 |2 |2 × 500 |1 / måttlig |
| Standard_F2 |2 |4 |32 |4 |4 × 500 |2 / hög |
| Standard_F4 |4 |8 |64 |8 |8 × 500 |4 / hög |
| Standard_F8 |8 |16 |128 |16 |16 × 500 |8 / hög |
| Standard_F16 |16 |32 |256 |32 |32 ×&500; |8 / extremt hög |

<br>

## <a name="fs-series"></a>Fs-serien*
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt cachelagrat diskgenomflöde: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_F1s |1 |2 |4 |2 |4,000 / 32 (12) |3,200 / 48 |1 / måttlig |
| Standard_F2s |2 |4 |8 |4 |8,000 / 64 (24) |6,400 / 96 |2 / hög |
| Standard_F4s |4 |8 |16 |8 |16,000 / 128 (48) |12,800 / 192 |4 / hög |
| Standard_F8s |8 |16 |32 |16 |32,000 / 256 (96) |25,600 / 384 |8 / hög |
| Standard_F16s |16 |32 |64 |32 |64,000 / 512 (192) |51,200 / 768 |8 / extremt hög |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i Fs-serien kan begränsas av de anslutna diskarnas antal, storlek och striping.  Mer information finns i [Premium Storage: Lagring med höga prestanda för arbetsbelastningar på virtuella datorer i Azure](../articles/storage/storage-premium-storage.md).

<br>

## <a name="g-series"></a>G-serien
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 × 500 |1 / hög |
| Standard_G2 |4 |56 |768 |8 |8 × 500 |2 / hög |
| Standard_G3 |8 |112 |1,536 |16 |16 × 500 |4 / mycket hög |
| Standard_G4 |16 |224 |3,072 |32 |32 × 500 |8 / extremt hög |
| Standard_G5* |32 |448 |6,144 |64 |64 × 500 |8 / extremt hög |

*Instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>

## <a name="gs-series"></a>GS-serien*
| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt cachelagrat diskgenomflöde: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_GS1 |2 |28 |56 |4 |10,000 / 100 (264) |5,000 / 125 |1 / hög |
| Standard_GS2 |4 |56 |112 |8 |20,000 / 200 (528) |10,000 / 250 |2 / hög |
| Standard_GS3 |8 |112 |224 |16 |40,000 / 400 (1,056) |20,000 / 500 |4 / mycket hög |
| Standard_GS4 |16 |224 |448 |32 |80,000 / 800 (2,112) |40,000 / 1,000 |8 / extremt hög |
| Standard_GS5** |32 |448 |896 |64 |160,000 / 1,600 (4,224) |80,000 / 2,000 |8 / extremt hög |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

*Det maximala diskgenomflödet (IOPS eller Mbit/s) som är möjligt med virtuella datorer i GS-serien kan begränsas av de anslutna diskarnas antal, storlek och striping. 

**Instansen är isolerad till maskinvara som är dedikerad till en enda kund.
<br>

## <a name="h-series"></a>H-serien
Virtuella datorer i Azure H-serien är nästa generations virtuella datorer för databehandling med höga prestanda och är avsedda för höga beräkningsbehov, som molekylär modellering och beräkningsströmningsdynamik. Dessa virtuella datorer med 8 och 16 kärnor bygger på Intel Haswell E5-2667 V3-processortekniken med DDR4-minne och lokal SSD-baserad lagring. 

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.

Mer information och saker att tänka på när du använder dessa storlekar finns i avsnittet [om H-serien och beräkningsintensiva virtuella datorer i A-serien](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort/nätverksbandbredd |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 × 500 |2 / hög |
| Standard_H16 |16 |112 |2000 |32 |32 × 500 |4 / mycket hög |
| Standard_H8m |8 |112 |1000 |16 |16 × 500 |2 / hög |
| Standard_H16m |16 |224 |2000 |32 |32 × 500 |4 / mycket hög |
| Standard_H16r* |16 |112 |2000 |32 |32 × 500 |4 / mycket hög |
| Standard_H16mr* |16 |224 |2000 |32 |32 × 500 |4 / mycket hög |

*RDMA-stöd

<br>


## <a name="ls-series"></a>Ls-serien 

Ls-serien är optimerad för arbetsbelastningar som kräver lokal lagring med låg svarstid, som NoSQL-databaser (t.ex. Cassandra, MongoDB, Cloudera och Redis). Ls-serien ger upp till 32 processorkärnor med hjälp av den [Intel® Xeon®-processorns E5 v3-familj](http://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Det här är samma CPU-prestanda som G/GS-serien och kommer med 8 GiB minne per CPU-kärna.  

 
| Storlek          | Processorkärnor | Minne: GiB | Lokal SSD: GiB | Maximalt antal datadiskar | Maximalt cachelagrat diskgenomflöde: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd | 
|---------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standard_L4s  | 4    | 32   | 678   | 8              | NA / NA (0)          | 5,000 / 125                               | 2 / hög       | 
| Standard_L8s  | 8    | 64   | 1,388 | 16             | NA / NA (0)          | 10,000 / 250                              | 4 / mycket hög  | 
| Standard_L16s | 16   | 128  | 2,807 | 32             | NA / NA (0)          | 20,000 / 500                              | 8 / extremt hög | 
| Standard_L32s | 32   | 256  | 5,630 | 64             | NA / NA (0)          | 40,000 / 1,000                            | 8 / extremt hög | 
 
Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte. 



## <a name="n-series"></a>N-serien
NC- och NV-storlekarna kallas även GPU-aktiverade instanser. De är specialiserade virtuella datorer som använder NVIDIA:s GPU-kort och som är optimerade för olika scenarier och användningsfall. NV-storlekarna är optimerade och utformade för fjärrvisualiserings-, strömnings-, spel-, kodnings- och VDI-scenarier med ramverk som OpenGL och DirectX. NC-storlekarna är mer optimerade för beräkningsintensiva och nätverksintensiva tillämpningar och algoritmer, inklusive CUDA- och OpenCL-baserade program och simuleringar. 


### <a name="nv-instances"></a>NV-instanser
NV-instanserna drivs av NVIDIA:s Tesla M60 GPU-kort och NVIDIA GRID för skrivbordsaccelererade program och virtuella skrivbord där kunder kan visualisera sina data eller simuleringar. Användarna kan visualisera grafikintensiva arbetsflöden på NV-instanserna och få överlägsen grafikkapacitet samt köra enskilda precisionsarbetsbelastningar som kodning och rendering. Tesla M60 levererar 4 096 CUDA-kärnor i en design med dubbla GPU:er med upp till 36 dataströmmar med 1080p H.264. 


| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 |
| Standard_NV12 |12 |112 |680 | 2 |
| Standard_NV24 |24 |224 |1440 | 4 |

1 GPU = ett halvt M60-kort.

**Operativsystem som stöds**

* Windows Server 2016, Windows Server 2012 R2 – läs avsnittet om [drivrutinsinstallationer för N-serien för Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)

### <a name="nc-instances"></a>NC-instanser
NC-instanserna drivs av NVIDIA:s Tesla K80-kort. Nu kan användarna bearbeta data mycket snabbare genom att utnyttja CUDA för energiutforskningstillämpningar, kraschsimulering, strålspårning (raytracing), deep learning och mycket annat. Tesla K80 levererar 4 992 CUDA-kärnor med en design med dubbla GPU:er, upp till 2,91 teraflops med dubbel precision och upp till 8,93 teraflops prestanda med enkel precision.

| Storlek | Processorkärnor | Minne: GiB | Lokal SSD: GiB | GPU |
| --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 |
| Standard_NC12 |12 |112 | 680 | 2 |
| Standard_NC24 |24 |224 | 1440 | 4 |
| Standard_NC24r* |24 |224 | 1440 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd

**Operativsystem som stöds**

* Windows Server 2016, Windows Server 2012 R2 – läs avsnittet om [drivrutinsinstallationer för N-serien för Windows](../articles/virtual-machines/virtual-machines-windows-n-series-driver-setup.md)
* Ubuntu 16.04 LTS – läs avsnittet om [drivrutinsinstallationer för N-serien för Linux](../articles/virtual-machines/virtual-machines-linux-n-series-driver-setup.md)

<br>

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Kommentarer: Standard A0 – A4 med CLI och PowerShell
I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall 
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azure-prenumerationer, tjänstbegränsningar, kvoter och krav](../articles/azure-subscription-service-limits.md).
* Lär dig mer [om H-serien och beräkningsintensiva virtuella datorer i A-serien](../articles/virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för arbetsbelastningar som HPC-arbetsbelastningar för databehandling med höga prestanda.



<!--HONumber=Feb17_HO3-->


