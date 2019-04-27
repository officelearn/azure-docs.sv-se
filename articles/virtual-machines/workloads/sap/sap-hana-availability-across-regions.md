---
title: Tillgänglighet för SAP HANA i Azure-regioner | Microsoft Docs
description: En översikt över tillgänglighet att tänka på när du kör SAP HANA på Azure virtuella datorer i flera Azure-regioner.
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
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 95ada2cb146bdbc972afee883a1d174c95aa67d7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60650308"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Tillgänglighet för SAP HANA i Azure-regioner

Den här artikeln beskrivs scenarier som rör tillgängligheten för SAP HANA i olika Azure-regioner. På grund av avståndet mellan Azure-regioner innebär att tänka på att ställa in tillgänglighet för SAP HANA i flera Azure-regioner.

## <a name="why-deploy-across-multiple-azure-regions"></a>Varför ska du distribuera i flera Azure-regioner

Azure-regioner är ofta åtskilda av stora avstånd. Beroende på den geopolitiska regionen kan avståndet mellan Azure-regioner vara långt eller även flera tusen miles som i USA. På grund av avstånd uppleva nätverkstrafik mellan tillgångar som distribueras i två olika Azure-regioner betydande tur och RETUR Nätverksfördröjningen. Svarstiden är tillräckligt stor för att undanta synkront datautbyte mellan två SAP HANA-instanser under vanliga SAP-arbetsbelastningar. 

Organisationer har å andra sidan, ofta ett krav för avståndet mellan platsen för det primära datacentret och ett sekundärt datacenter. En avståndet krav bidrar till att ge tillgänglighet om en naturkatastrof inträffar i en bredare geografisk plats. Exempel innefattar orkaner som når Karibien och Florida i September och oktober 2017. Din organisation kan ha minst ett minsta avstånd krav. För de flesta Azure-kunder en definition av minsta avstånd som kräver att du utformar för tillgänglighet över [Azure-regioner](https://azure.microsoft.com/regions/). Eftersom avståndet mellan två Azure-regioner är för stor för att använda läget för HANA synkron replikering, kan RTO och RPO krav tvinga dig att distribuera tillgänglighet konfigurationer i en region och sedan komplettera med ytterligare distributioner på en sekund region.

En annan aspekt att tänka på i det här scenariot är redundans och dirigera om klienten. Antas att växling mellan SAP HANA-instanser i två olika Azure-regioner alltid är en manuell redundansväxling. Eftersom replikeringsläge för SAP HANA-systemreplikering anges för asynkron, finns det risk att data som allokerat i den primära HANA-instansen inte har ännu tog sig till den sekundära HANA-instansen. Därför automatisk redundans är inte ett alternativ för konfigurationer där replikeringen är asynkrona. Även med manuellt kontrollerad redundansväxling, som i ett failover-övningen måste du vidta åtgärder för att se till att alla allokerade data på den primära sidan den till den sekundära instansen innan du manuellt flytta över till andra Azure-region.
 
Azure-nätverk använder en annan IP-adressintervall. IP-adresser har distribuerats i andra Azure-region. Därför antingen måste du ändra konfigurationen för SAP HANA-klienten eller helst måste du skapa steg om du vill ändra namnmatchningen. På så sätt kan omdirigeras klienter till den nya sekundära platsen serverns IP-adress. Mer information finns i artikeln SAP [klienten anslutning återställning efter ett övertagande av](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Enkel tillgänglighet mellan två Azure-regioner

Du kan välja inte att upprätta någon konfiguration för tillgänglighet inom en enda region och har fortfarande begäran ha arbetsbelastningen hanteras om en olycka inträffar. Typiska fall sådana scenarier är icke-produktionsmiljöer system. Även om det är hållbar med systemet ned för en halv dag eller ännu en dag, kan du tillåta systemet var tillgänglig i 48 timmar eller mer. Kör ett annat system som är även mindre viktiga på den virtuella datorn för att göra installationen blir kostnadseffektivare. Det andra systemet fungerar som ett mål. Du kan också ändra storlek på den virtuella datorn i den sekundära regionen ska vara mindre och väljer att inte förinstallera data. Eftersom växling vid fel är manuell och innebär många fler steg för att växla över hela appen stack, är extra tid att Stäng av den virtuella datorn, ändra storlek på den och sedan starta om den virtuella datorn acceptabelt.

Om du använder oss av delar DR-målet med en QA-dator i en virtuell dator kan behöva du dessa tänka:

- Det finns två [arbetslägen](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) med delta_datashipping och logreplay, som är tillgängliga för ett sådant scenario
- Båda arbetslägen har olika minneskrav utan förinläsning av data
- Delta_datashipping kan kräva drastiskt mindre minne utan alternativet true än logreplay kan det krävas. Se kapitel 4.3 av SAP-dokumentet [hur du utför System Replication för SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Minneskravet för logreplay arbetsläge utan Förhandsladda är inte deterministisk och beror på inlästa columnstore strukturer. Du kan behöva 50% av minnet för den primära instansen i extrema fall. Minne för logreplay arbetsläge är oberoende på om du har valt att förinlästa informationen eller inte.


![Diagram över två regioner på två virtuella datorer](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> I den här konfigurationen kan du inte ange ett Återställningpunktsmål = 0 eftersom din HANA system replication läge är asynkrona. Om du måste ange ett Återställningpunktsmål = 0, den här konfigurationen är inte konfigurationen av val.

Små ändringar som du kan göra i konfigurationen är att konfigurera data som förinläsning. Men kan den manuellt naturen för redundans och det faktum att programskikt också behöva flytta till den andra regionen, den inte vara klokt att förinstallera data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinera tillgänglighet inom en region och mellan regioner 

En kombination av tillgänglighet inom och mellan regioner kan styras av dessa faktorer:

- Ett krav för Återställningspunktmålet = 0 inom en Azure-region.
- Organisationen är inte vill eller kan ha globala åtgärder som påverkas av en större naturlig allvarliga händelser som påverkar en större region. Detta var fallet för vissa orkaner som når Karibien de senaste åren.
- Förordningar som begäran sträckor mellan primära och sekundära platser som är tydligt utöver vad Azure availability zones kan ge.

I dessa fall kan du kan konfigurera vilka SAP-anrop en [replikeringskonfigurationen för SAP HANA-skikt system](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) med hjälp av HANA-systemreplikering. Arkitekturen ser ut som:

![Diagram över tre virtuella datorer över två regioner](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introduceras [flera målsystemet replikering](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) med HANA 2.0 SPS3. Flera målsystemet replikering skyddar vissa fördelar i uppdateringsscenarier. Till exempel påverkas inte DR-plats (Region 2) när den sekundära platsen för hög tillgänglighet är nere för underhåll och uppdateringar. Du hittar mer information om replikering av HANA flera målsystemet [här](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Möjlig arkitektur med flera mål replikering ser ut som:

![Diagram över tre virtuella datorer över två regioner milti-mål](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Om organisationen har krav för beredskap för hög tillgänglighet i second(DR) Azure-region, ser arkitekturen ut som:

![Diagram över tre virtuella datorer över två regioner milti-mål](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Med hjälp av logreplay som arbetsläge för den här konfigurationen ger ett RPO = 0, med lågt RTO inom den primära regionen. Konfigurationen innehåller också vettigt RPO om en övergång till den andra regionen som ingår. RTO gånger i den andra regionen som är beroende av om data är förinstallerade. Många kunder använder den virtuella datorn i den sekundära regionen för att köra ett testsystem. I som användningsfall, det går inte att i förväg data.

> [!IMPORTANT]
> Arbetslägen mellan de olika nivåerna måste vara homogena. Du **kan** använda logreply som arbetsläge mellan 1 och nivå 2 och delta_datashipping Ange nivå 3. Du kan bara välja den eller de andra arbetsläge som måste vara konsekvent för alla nivåer. Eftersom delta_datashipping inte är lämpligt att ge dig en RPO = 0, endast rimliga arbetsläge för sådan flerskiktade konfiguration förblir logreplay. Mer information om arbetslägen och vissa begränsningar, finns i SAP-artikeln [arbetslägen för SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Nästa steg

Stegvisa anvisningar om hur du konfigurerar de här konfigurationerna i Azure finns i:

- [Konfigurera SAP HANA-systemreplikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
