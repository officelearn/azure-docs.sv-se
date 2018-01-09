---
title: "Virtuell storlek för Azure-molntjänster | Microsoft Docs"
description: "Visar en lista över storlekar för olika virtuella datorer (och ID: N) för Azure cloud service webb- och arbetsroller roller."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: a5ac8c46f17d2d1c2f20ed2cc2348f50b7739ddf
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="sizes-for-cloud-services"></a>Storlekar för molntjänster
Det här avsnittet beskrivs tillgängliga storlekar och alternativ för Molntjänsten rollinstanser (webb- och arbetsroller). Det ger också överväganden vid distribution för att vara medveten om när du planerar att använda dessa resurser. Varje storlek har ett ID som du lägger till i din [tjänstdefinitionsfilen](cloud-services-model-and-package.md#csdef). Priser för varje storlek är tillgängliga på den [Cloud Services priser](https://azure.microsoft.com/pricing/details/cloud-services/) sidan.

> [!NOTE]
> Relaterade Azure gränser finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md)
>
>

## <a name="sizes-for-web-and-worker-role-instances"></a>Storlekar för webb- och arbetsroller rollinstanser
Du kan välja mellan flera standardstorlekar i Azure. Det finns några saker som du bör tänka på när du väljer storlek:

* Virtuella datorer i D-serien är utformade för att köra program som kräver högre beräkningskraft och tillfälliga diskprestanda. Virtuella datorer i D-serien erbjuder snabbare processorer, högre ”minne till kärna”-förhållande och en Solid State-hårddisk (SSD) för den tillfälliga disken. Mer information finns i inlägget om [nya storlekar för virtuella datorer i D-serien](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) i Azure-bloggen.
* Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den baseras på den senaste generationens 2,4 GHz Intel Xeon® E5-2673 v3-processor (Haswell) och kan uppnå 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.
* Virtuella datorer i G-serien erbjuder mest minne och körs på värdar som är utrustade med processorer i Intel Xeon E5 V3-familjen.
* A-series virtuella datorer kan distribueras på olika maskinvarutyper och processorer. Storleken begränsas baserat på vilken maskinvara, att erbjuda konsekvent processorprestanda för instansen som körs, oavsett maskinvara som den har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.
* A0-storleken har för hög andel prenumerationer på den fysiska maskinvaran. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

Storleken på den virtuella datorn påverkar priset. Storleken påverkar också den virtuella datorns bearbetnings-, minnes- och lagringskapacitet. Lagringskostnaderna beräknas separat baserat på använda sidor i lagringskontot. Mer information finns i [prisinformation för Cloud Services](https://azure.microsoft.com/pricing/details/cloud-services/) och [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Följande information kan hjälpa dig att välja storlek:

* Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz. Detaljerad information och överväganden om hur du använder dessa storlekar finns [högpresterande compute VM-storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Dv2-serien, D-serien, G-serien, lämpar sig för program som kräver snabbare processorer, bättre lokal disk prestanda eller har högre krav på minne. De utgör en kraftfull kombination för många program i företagsklass.
* Vissa av de fysiska värdarna i Azure-datacenter kanske inte stöder större storlekar för virtuell dator, till exempel A5–A11. Därför kan det hända att felmeddelandet **det gick inte att konfigurera den virtuella datorn {datornamnet}** eller **det gick inte att skapa den virtuella datorn {datornamnet}** när du ändrar storlek på en befintlig virtuell dator till en ny storlek; Skapa en ny virtuell dator i ett virtuellt nätverk som skapats före 16 April 2013; eller lägga till en ny virtuell dator till en befintlig molntjänst. Se [fel: ”Det gick inte att konfigurera den virtuella datorn”](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) på Supportforum för lösningar för varje scenario för distribution.
* Din prenumeration kan även begränsa hur många kärnor som du kan distribuera i vissa storleksfamiljer. Kontakta Azure-supporten om du vill öka en kvot.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda
Vi har skapat konceptet för den Azure Compute-enhet (ACU) att ge ett sätt för att jämföra bearbetningsprestanda (CPU) över Azure SKU: er och för att identifiera vilket SKU är sannolikt att uppfylla dina prestanda måste.  ACU är för närvarande standardiserat på en liten virtuell dator (Standard_A1) och är 100, och alla andra SKU:er representerar ungefär hur mycket snabbare den SKU:n kan köra ett benchmark-standardtest.

> [!IMPORTANT]
> ACU är endast en riktlinje. Resultatet med din arbetsbelastning kan variera.
>
>

<br>

| SKU-familj | ACU/kärna |
| --- | --- |
| [ExtraSmall](#a-series) |50 |
| [Liten Extrastora](#a-series) |100 |
| [A5 7](#a-series) |100 |
| [Standard_A1-8v2](#av2-series) |100 |
| [Standard_A2m-8mv2](#av2-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [G1-5](#g-series) |180 - 240* |
| [H](#h-series) |290 - 300* |

ACU:er som visas med * använder Intel® Turbo-teknik för att öka processorfrekvensen och prestanda. Prestandaökningens storlek kan variera beroende på storleken på den virtuella datorn, arbetsbelastningen och andra arbetsbelastningar som körs på samma värd.

## <a name="size-tables"></a>Storlekstabeller
Följande tabeller visar storlekarna och den kapacitet som de tillhandahåller.

* Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
* Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
* Datadiskar kan köras i cachelagrat eller icke cachelagrat läge. För diskåtgärder med cachelagrade data anges cacheläget till **ReadOnly** eller **ReadWrite**. För diskåtgärder med icke cachelagrade data anges cacheläget till **Inget**.
* Den maximala nätverksbandbredden är den högsta aggregerade bandbredden som allokeras och tilldelas per typ av virtuell dator. Den maximala bandbredden är en riktlinje som hjälper dig att välja rätt typ av virtuell dator för att säkerställa tillräcklig nätverkskapacitet. Genomflödet ökar när du flyttar mellan låg, Måttlig, hög och mycket hög. Faktiska nätverksprestanda beror på många faktorer, bland annat nätverks- och programbelastningar och programmets nätverksinställningar.

## <a name="a-series"></a>A-serien
| Storlek            | Processorkärnor | Minne: GiB  | Lokal hårddisk: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| ExtraSmall      | 1         | 0.768        | 20                   | 1 / låg |
| Liten           | 1         | 1.75         | 225                  | 1 / måttlig |
| Medel          | 2         | 3,5 GB       | 490                  | 1 / måttlig |
| Stor           | 4         | 7            | 1000                 | 2 / hög |
| Extrastora      | 8         | 14           | 2040                 | 4 / hög |
| A5              | 2         | 14           | 490                  | 1 / måttlig |
| A6              | 4         | 28           | 1000                 | 2 / hög |
| A7              | 8         | 56           | 2040                 | 4 / hög |

## <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser
Mer information och överväganden om hur du använder dessa storlekar finns [högpresterande compute VM-storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Storlek            | Processorkärnor | Minne: GiB  | Lokal hårddisk: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| A8 *             |8          | 56           | 1817                 | 2 / hög |
| A9 *             |16         | 112          | 1817                 | 4 / mycket hög |
| A10             |8          | 56           | 1817                 | 2 / hög |
| A11             |16         | 112          | 1817                 | 4 / mycket hög |

\*RDMA-kompatibla

## <a name="av2-series"></a>Av2-serien

| Storlek            | Processorkärnor | Minne: GiB  | Lokal SSD: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_A1_v2  | 1         | 2            | 10                   | 1 / måttlig                 |
| Standard_A2_v2  | 2         | 4            | 20                   | 2 / måttlig                 |
| Standard_A4_v2  | 4         | 8            | 40                   | 4 / hög                     |
| Standard_A8_v2  | 8         | 16           | 80                   | 8 / hög                     |
| Standard_A2m_v2 | 2         | 16           | 20                   | 2 / måttlig                 |
| Standard_A4m_v2 | 4         | 32           | 40                   | 4 / hög                     |
| Standard_A8m_v2 | 8         | 64           | 80                   | 8 / hög                     |


## <a name="d-series"></a>D-serien
| Storlek            | Processorkärnor | Minne: GiB  | Lokal SSD: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1     | 1         | 3.5          | 50                   | 1 / måttlig |
| Standard_D2     | 2         | 7            | 100                  | 2 / hög |
| Standard_D3     | 4         | 14           | 200                  | 4 / hög |
| Standard_D4     | 8         | 28           | 400                  | 8 / hög |
| Standard_D11    | 2         | 14           | 100                  | 2 / hög |
| Standard_D12    | 4         | 28           | 200                  | 4 / hög |
| Standard_D13    | 8         | 56           | 400                  | 8 / hög |
| Standard_D14    | 16        | 112          | 800                  | 8 / mycket hög |

## <a name="dv2-series"></a>Dv2-serien
| Storlek            | Processorkärnor | Minne: GiB  | Lokal SSD: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_D1_v2  | 1         | 3.5          | 50                   | 1 / måttlig |
| Standard_D2_v2  | 2         | 7            | 100                  | 2 / hög |
| Standard_D3_v2  | 4         | 14           | 200                  | 4 / hög |
| Standard_D4_v2  | 8         | 28           | 400                  | 8 / hög |
| Standard_D5_v2  | 16        | 56           | 800                  | 8 / extremt hög |
| Standard_D11_v2 | 2         | 14           | 100                  | 2 / hög |
| Standard_D12_v2 | 4         | 28           | 200                  | 4 / hög |
| Standard_D13_v2 | 8         | 56           | 400                  | 8 / hög |
| Standard_D14_v2 | 16        | 112          | 800                  | 8 / extremt hög |
| Standard_D15_v2 | 20        | 140          | 1,000                | 8 / extremt hög |

## <a name="g-series"></a>G-serien
| Storlek            | Processorkärnor | Minne: GiB  | Lokal SSD: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_G1     | 2         | 28           | 384                  |1 / hög |
| Standard_G2     | 4         | 56           | 768                  |2 / hög |
| Standard_G3     | 8         | 112          | 1,536                |4 / mycket hög |
| Standard_G4     | 16        | 224          | 3,072                |8 / extremt hög |
| Standard_G5     | 32        | 448          | 6,144                |8 / extremt hög |

## <a name="h-series"></a>H-serien
Virtuella datorer i Azure H-serien är nästa generations virtuella datorer för databehandling med höga prestanda och är avsedda för höga beräkningsbehov, som molekylär modellering och beräkningsströmningsdynamik. Dessa 8 och 16 kärnor VMs bygger på Intel Haswell E5 2667 V3-processor-teknik med DDR4 minne och lokala SSD-baserad lagring.

Förutom den imponerande processorkraften erbjuder H-serien olika alternativ för RDMA-nätverk med låg fördröjning med FDR InfiniBand och flera minneskonfigurationer som ger stöd för minnesintensiva beräkningskrav.

| Storlek            | Processorkärnor | Minne: GiB  | Lokal SSD: GiB       | Maximalt antal nätverkskort/nätverksbandbredd |
|---------------- | --------- | ------------ | -------------------- | ---------------------------- |
| Standard_H8     | 8         | 56           | 1000                 | 8 / hög |
| Standard_H16    | 16        | 112          | 2000                 | 8 / mycket hög |
| Standard_H8m    | 8         | 112          | 1000                 | 8 / hög |
| Standard_H16m   | 16        | 224          | 2000                 | 8 / mycket hög |
| Standard_H16r*  | 16        | 112          | 2000                 | 8 / mycket hög |
| Standard_H16mr* | 16        | 224          | 2000                 | 8 / mycket hög |

\*RDMA-kompatibla

## <a name="configure-sizes-for-cloud-services"></a>Konfigurera storlekar för molntjänster
Du kan ange storleken på virtuella datorn på en rollinstans av som en del av den tjänstmodell som beskrivs i den [tjänstdefinitionsfilen](cloud-services-model-and-package.md#csdef). Storleken på rollen som anger antalet CPU-kärnor, minneskapacitet och lokala system filstorleken som allokerats till en instans som körs. Välj rollstorleken på baserat på ditt program resurskrav.

Här är ett exempel för att ställa in rollstorleken ska [Standard_D2](#general-purpose-d) för en Webbroll-instans:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="changing-the-size-of-an-existing-role"></a>Ändra storlek på en befintlig roll

Som typ av arbetsbelastning ändringar eller nya VM-storlekar som blir tillgängliga, kanske du vill ändra storleken på din roll. Om du vill göra det, måste du ändra storleken på virtuella datorn i definitionsfilen för tjänsten (som visas ovan) måste paketera din molntjänst och distribuera det. Det går inte att ändra VM-storlekar direkt från portalen eller PowerShell.

>[!TIP]
> Du kanske vill använda olika VM-storlek för din roll i olika miljöer (t.ex. Testa vs produktion). Ett sätt att göra detta är att skapa flera tjänstdefinitionen (.csdef) filer i ditt projekt skapas annat moln servicepaket per miljö under din automatiserade build med hjälp av verktyget CSPack. Mer information om elementen i ett paket för cloud services och hur du skapar dem finns [vad är molnet services modellen och hur jag paketera den?](cloud-services-model-and-package.md)
>
>

## <a name="get-a-list-of-sizes"></a>Hämta en lista över storlekar
Du kan använda PowerShell eller REST API för att hämta en lista över storlekar. REST API dokumenteras [här](https://msdn.microsoft.com/library/azure/dn469422.aspx). Följande kod är ett PowerShell-kommando som visar en lista över alla storlekar för en viss plats. 

```powershell
Get-AzureRmVMSize -Location 'West Europe'
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azure-prenumerationer, tjänstbegränsningar, kvoter och krav](../azure-subscription-service-limits.md).
* Lär dig mer [om högpresterande compute VM-storlekar](../virtual-machines/windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) för HPC-arbetsbelastning.
