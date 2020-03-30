---
title: SAP HANA-tillgänglighet i Azure-regioner | Microsoft-dokument
description: En översikt över tillgänglighetsöverväganden när du kör SAP HANA på virtuella Azure-datorer i flera Azure-regioner.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a393865038722f2fd7fa5e42334f8d5e760951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70078858"
---
# <a name="sap-hana-availability-across-azure-regions"></a>SAP HANA-tillgänglighet i Azure-regioner

I den här artikeln beskrivs scenarier som är relaterade till SAP HANA-tillgänglighet i olika Azure-regioner. På grund av avståndet mellan Azure-regioner innebär det särskilda överväganden om att konfigurera SAP HANA-tillgänglighet i flera Azure-regioner.

## <a name="why-deploy-across-multiple-azure-regions"></a>Varför distribuera över flera Azure-regioner

Azure-regioner skiljs ofta åt av stora avstånd. Beroende på den geopolitiska regionen kan avståndet mellan Azure-regioner vara hundratals miles, eller till och med flera tusen miles, som i USA. På grund av avståndet upplever nätverkstrafik mellan tillgångar som distribueras i två olika Azure-regioner betydande svarstid för nätverksresor. Svarstiden är tillräckligt viktig för att utesluta synkront datautbyte mellan två SAP HANA-instanser under typiska SAP-arbetsbelastningar. 

Å andra sidan har organisationer ofta ett avståndskrav mellan platsen för det primära datacentret och ett sekundärt datacenter. Ett avståndskrav bidrar till att ge tillgänglighet om en naturkatastrof inträffar på en bredare geografisk plats. Exempel är orkanerna som drabbade Karibien och Florida i september och oktober 2017. Din organisation kan ha minst ett minsta avståndskrav. För de flesta Azure-kunder kräver en minsta avståndsdefinition att du utformar för tillgänglighet i [Azure-regioner](https://azure.microsoft.com/regions/). Eftersom avståndet mellan två Azure-regioner är för stort för att använda HANA-synkrona replikeringsläget kan RTO- och RPO-krav tvinga dig att distribuera tillgänglighetskonfigurationer i en region och sedan komplettera med ytterligare distributioner på en sekund Regionen.

En annan aspekt att tänka på i det här scenariot är redundans och klientomdirigering. Antagandet är att en redundans mellan SAP HANA-instanser i två olika Azure-regioner alltid är en manuell redundans. Eftersom replikeringsläget för SAP HANA-systemreplikering är inställt på asynkron, finns det en potential att data som begåtts i den primära HANA-instansen ännu inte har gjort det till den sekundära HANA-instansen. Därför är automatisk redundans inte ett alternativ för konfigurationer där replikeringen är asynkron. Även med manuellt kontrollerad redundans, som i en redundansövning, måste du vidta åtgärder för att säkerställa att alla bekräftade data på den primära sidan gjorde det till den sekundära instansen innan du manuellt flyttar över till den andra Azure-regionen.
 
Azure Virtual Network använder ett annat IP-adressintervall. IP-adresserna distribueras i den andra Azure-regionen. Så du måste antingen ändra SAP HANA-klientkonfigurationen, eller helst måste du skapa steg för att ändra namnmatchningen. På så sätt omdirigeras klienterna till den nya sekundära platsens server-IP-adress. Mer information finns i SAP-artikeln [Klientanslutningsåterställning efter övertagande](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Enkel tillgänglighet mellan två Azure-regioner

Du kan välja att inte införa någon tillgänglighetskonfiguration inom en enda region, men ändå ha efterfrågan på att arbetsbelastningen ska hanteras om en katastrof inträffar. Typiska fall för sådana scenarier är icke-produktionssystem. Även om att ha systemet nere i en halv dag eller ens en dag är hållbart, kan du inte tillåta att systemet inte är tillgängligt i 48 timmar eller mer. För att göra installationen billigare, kör ett annat system som är ännu mindre viktigt i den virtuella datorn. Det andra systemet fungerar som en destination. Du kan också ändra storleken på den virtuella datorn i den sekundära regionen så att den blir mindre och välja att inte förinlästa data. Eftersom redundansen är manuell och innebär många fler steg för att växla över hela programstacken, är den extra tiden för att stänga av den virtuella datorn, ändra storlek på den och sedan starta om den virtuella datorn acceptabelt.

Om du använder scenariot med att dela DR-målet med ett QA-system i en virtuell dator måste du ta hänsyn till dessa överväganden:

- Det finns två [driftlägen](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) med delta_datashipping och logreplay, som är tillgängliga för ett sådant scenario
- Båda driftlägena har olika minneskrav utan att förinläsa data
- Delta_datashipping kan kräva drastiskt mindre minne utan förinläsningsalternativet än logreplay kan kräva. Se kapitel 4.3 i SAP-dokumentet [Hur du utför systemreplikering för SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Minnesbehovet för logreplay-driftläge utan förinläsning är inte deterministiskt och beror på de columnstore-strukturer som läses in. I extrema fall kan du kräva 50% av minnet av den primära instansen. Minnet för logreplay-åtgärdsläge är oberoende av om du har valt att ha data förinstallerad eller inte.


![Diagram över två virtuella datorer över två regioner](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> I den här konfigurationen kan du inte ange en RPO=0 eftersom hana-systemreplikeringsläget är asynkront. Om du behöver ange en RPO=0 är den här konfigurationen inte den konfiguration som du väljer.

En liten ändring som du kan göra i konfigurationen kan vara att konfigurera data som förinläsning. Med tanke på redundansens manuella karaktär och det faktum att programlager också behöver flyttas till den andra regionen, kanske det inte är meningsfullt att förinläsa data. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Kombinera tillgänglighet inom en region och mellan regioner 

En kombination av tillgänglighet inom och mellan regioner kan drivas av dessa faktorer:

- Ett krav på RPO=0 inom en Azure-region.
- Organisationen är inte villig eller kan ha globala operationer som påverkas av en stor naturkatastrof som påverkar en större region. Detta var fallet för några orkaner som drabbade Karibien under de senaste åren.
- Regler som kräver avstånd mellan primära och sekundära platser som är klart utöver vad Azure-tillgänglighetszoner kan tillhandahålla.

I dessa fall kan du ställa in vad SAP anropar en [SAP HANA multitier systemreplikeringskonfiguration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) med hjälp av HANA-systemreplikering. Arkitekturen skulle se ut så här:

![Diagram över tre virtuella datorer över två regioner](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introducerade [flermålssystemreplikering](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) med HANA 2.0 SPS3. Systemreplikering med flera mål ger vissa fördelar i uppdateringsscenarier. DR-platsen (region 2) påverkas till exempel inte när den sekundära HA-platsen är nere för underhåll eller uppdateringar. Du kan läsa mer om HANA multi-target system replikering [här](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Möjlig arkitektur med replikering med flera mål skulle se ut:

![Diagram över tre virtuella datorer över två regioner milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Om organisationen har krav på hög tillgänglighetsberedskap i azure-regionen second(DR) skulle arkitekturen se ut:

![Diagram över tre virtuella datorer över två regioner milti-target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Med logreplay som driftläge ger den här konfigurationen en RPO=0, med låg RTO, inom den primära regionen. Konfigurationen ger också anständiga RPO om en flytt till den andra regionen är inblandad. RTO-tiderna i den andra regionen är beroende av om data är förinläst. Många kunder använder den virtuella datorn i den sekundära regionen för att köra ett testsystem. I det användningsfallet kan data inte förinstalleras.

> [!IMPORTANT]
> Driftlägena mellan de olika nivåerna måste vara homogena. Du **kan inte** använda logreply som driftläge mellan nivå 1 och nivå 2 och delta_datashipping för att ange nivå 3. Du kan bara välja det eller de andra driftläget som måste vara konsekvent för alla nivåer. Eftersom delta_datashipping inte är lämpligt för att ge dig en RPO=0, förblir det enda rimliga driftläget för en sådan konfiguration på flera nivåer logreplay. Mer information om driftlägen och vissa begränsningar finns i [driftlägena för SAP-artikel för SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Nästa steg

Stegvis vägledning om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera SAP HANA-systemreplikering i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
