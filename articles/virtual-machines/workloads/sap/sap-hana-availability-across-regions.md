---
title: Tillgänglighet för SAP HANA över Azure-regioner | Microsoft Docs
description: En översikt över tillgänglighet överväganden när du kör SAP HANA på virtuella Azure-datorer i Azure-regioner.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/26/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: edbd1885dd529e4ccd38f2012d56865a2147f64d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-availability-across-azure-regions"></a>Tillgänglighet för SAP HANA över Azure-regioner

Den här artikeln beskrivs scenarier som rör tillgängligheten för SAP HANA mellan olika Azure-regioner. På grund av avståndet mellan Azure-regioner innebär att ställa in SAP HANA tillgänglighet i Azure-regioner speciella överväganden.

## <a name="why-deploy-across-multiple-azure-regions"></a>Varför distribuerar över flera Azure-regioner

Azure-regioner är ofta åtskilda av stora avstånd. Beroende på området geopolitiska kanske avståndet mellan Azure-regioner hundratals mil eller även flera tusen miles som i USA. På grund av avståndet upplevelse nätverkstrafik mellan tillgångar som har distribuerats i två olika Azure-regioner betydande roundtrip Nätverksfördröjningen. Svarstiden är viktiga för att utesluta synkront datautbyte mellan två instanser av SAP HANA under vanliga SAP-arbetsbelastningar. 

Organisationer har å andra sidan ofta ett krav för avståndet mellan platsen för det primära datacentret och ett sekundärt datacenter. Ett krav för avståndet ger tillgänglighet om en naturkatastrof uppstår i en större geografisk plats. Exempel: orkaner som träffar Västindien och Florida i September och oktober 2017. Din organisation ha minst ett krav på minsta avstånd. För de flesta Azure kunder, en definition av minsta avstånd som kräver att du att utforma för hög tillgänglighet över [Azure-regioner](https://azure.microsoft.com/regions/). Eftersom avståndet mellan två Azure-regioner är för stort för att använda HANA synkron replikeringsläge, RTO och Återställningspunktmål krav kan tvinga fram du distribuera tillgänglighet konfigurationer i en region och sedan komplettera med ytterligare distributioner på en sekund region.

En annan aspekt att tänka på i det här scenariot är redundans och dirigera om klienten. Förutsättningen är att växling mellan SAP HANA-instanser i två olika Azure-regioner alltid är en manuell växling vid fel. Eftersom replikering läget för SAP HANA system replication har angivits för asynkron, finns det risk att data i den primära HANA-instansen inte har ännu gjorde det till den sekundära HANA-instansen. Därför automatisk redundans är inte ett alternativ för konfigurationer där den replikeras asynkront. Du måste vidta åtgärder för att se till att alla data som utförts på den primära sidan gjorde det till den sekundära instansen innan du manuellt flytta till andra Azure-regionen även med manuellt kontrollerade redundans, som i ett failover-övningen.
 
Virtuella Azure-nätverket använder ett annat IP-adressintervall. IP-adresser har distribuerats i andra Azure-region. Så, antingen måste du ändra klientkonfigurationen för SAP HANA eller helst måste du skapa steg om du vill ändra namnmatchningen. På så sätt kan omdirigeras klienter till den nya sekundära platsen serverns IP-adress. Mer information finns i artikeln SAP [klienten anslutning återställning efter övertag](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Enkel tillgänglighet mellan två Azure-regioner

Du kan välja inte att upprätta någon konfiguration för tillgänglighet inom en enskild region, men har fortfarande begäran ha arbetsbelastningen hanteras om en olycka inträffar. Typiska fall då man system som det är icke-produktionsmiljöer system. Även om det är hållbara med systemet ned för en halv dag eller ännu en dag, kan du låta systemet otillgängligt 48 timmar eller mer. Om du vill att installationsprogrammet kostnadseffektivare kör ett annat system som är även mindre viktiga i den virtuella datorn. De andra systemfunktioner som mål. Du kan också ändra storlek på den virtuella datorn i den sekundära regionen ska vara mindre och väljer att inte förinläsas. Ytterligare tid för att stänga av den virtuella datorn, ändra storlek på den och sedan starta om den virtuella datorn är acceptabelt eftersom växling vid fel är manuell och innebär att många fler steg till hela appen stack-redundans.

> [!NOTE]
> Även om du inte använder data preload i replikeringsmålet HANA system, behöver du minst 64 GB minne. Du måste också tillräckligt med minne förutom 64 GB för att lagra rowstore-data i minnet på målinstansen.

![Diagram över två virtuella datorer över två områden](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> I den här konfigurationen kan du ange ett Återställningpunktsmål = 0 eftersom HANA system replication läget är asynkron. Om du måste ange ett Återställningpunktsmål = 0, den här konfigurationen är inte konfigurationen av valet.

En mindre ändring som du kan göra i konfigurationen kan vara att konfigurera data som förinläsning av. Men kan eftersom manuell växling vid fel och det faktum att programmet lager också behöva flytta till den andra regionen, det inte vara klokt att förinläsa data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinera tillgänglighet inom en region och över regioner 

En kombination av tillgänglighet inom och över regioner kan drivas av dessa faktorer:

- Ett krav för Återställningspunktmål = 0 i en Azure-region.
- Organisationen är inte vill eller kan ha globala åtgärder som påverkas av en större fysiska allvarlig händelse som påverkar ett större område. Detta är fallet för vissa orkaner som träffar Västindien under de senaste åren.
- Förordningar som begäran sträckor mellan primära och sekundära platser som är klart utöver vad Azure tillgänglighet zoner kan ge.

I dessa fall måste du ställa in vilka SAP-anrop en [replikeringskonfiguration för SAP HANA flera skikt system](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) med hjälp av HANA system replikering. Arkitekturen skulle se ut så här:

![Diagram över tre virtuella datorer över två områden](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

Den här konfigurationen tillhandahåller ett Återställningpunktsmål = 0, med låga RTO i den primära regionen. Konfigurationen innehåller också hyfsad Återställningspunktmål om en flytt till andra region ingår. Återställningstidsmålet gånger i andra region är beroende av om data är förinläst. Många kunder använder den virtuella datorn i den sekundära regionen för att köra ett testsystem. I som användningsfall, kan inte vara förinstallerade data.

> [!NOTE]
> Eftersom du använder **logreplay** arbetsläge för HANA system replikering går från nivå 1 till nivå 2 (synkron replikering i den primära regionen) replikering mellan nivå 2 och nivå 3 (replikering till den sekundära platsen) kan inte vara i **delta_datashipping** arbetsläge. Mer information om arbetslägen och vissa begränsningar finns i artikeln SAP [arbetslägen för SAP HANA system replikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Nästa steg

Stegvisa anvisningar om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera replikering för SAP HANA-system i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av system-replikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
