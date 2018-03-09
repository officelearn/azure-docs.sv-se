---
title: "SAP HANA tillgänglighet inom ett Azure-Region | Microsoft Docs"
description: "Åtgärder för SAP HANA på interna virtuella Azure-datorer"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA tillgänglighet inom en Azure-region
I det här avsnittet presenteras flera scenarier som beskrivs scenarier för tillgänglighet inom ett Azure-Region. Azure har många regioner som är utspridda över hela världen. Lista över Azure-regioner, finns det [Azure-regioner](https://azure.microsoft.com/regions/) artikel. Distribuera SAP HANA på virtuella datorer i en Azure-Region, erbjuder Microsoft distributionen av en enskild virtuell dator med en HANA-instans. Eller för ökad tillgänglighet kan du distribuera två virtuella datorer med två HANA instanser inom en [Azure Tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) som använder HANA System replikering för tillgänglighet. Azure har en offentlig förhandsgranskning av [Azure tillgänglighet zoner](https://docs.microsoft.com/azure/availability-zones/az-overview). Zonerna tillgänglighet kommer inte att diskuteras i detalj ännu. Förutom vissa allmänna tankar kring användning av Tillgänglighetsuppsättningar jämfört med tillgänglighet zoner.

Vad är skillnaden mellan Azure-Tillgänglighetsuppsättningar och tillgänglighet zoner? För Azure-regioner där tillgänglighet zoner kommer att erbjudas, har regionerna flera datacenter som är oberoende i tillhandahållande av kraftkälla kylning och nätverk. För att erbjuda olika zoner inom ett enda Azure-region beror på att du ska distribuera program över dra eller tre tillgänglighet zoner som erbjuds. Förutsatt att problem i strömkällor och/eller nätverket påverkar endast en tillgänglighet zonen-infrastruktur, fungerar programdistributionen i en Azure-region fortfarande. Slutligen kan med vissa minskad kapacitet eftersom vissa virtuella datorer i en zon gå förlorade. Men i de två zonerna är fortfarande aktiv och körs. 
 
En Azure Tillgänglighetsuppsättning är en logisk gruppering funktion som du kan använda i Azure för att säkerställa att VM-resurser som du placera inom är fel isoleras från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. Eller enligt vissa andra Azure-dokumentation, den kallas placeringar i olika [uppdatering och Feldomäner](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Dessa placeringar är vanligen inom ett Azure-datacenter. Förutsatt att problem i strömkällor och/eller nätverket kan påverka datacenter som du distribuerat, påverkas alla kapacitet i ett Azure-Region.

Placeringen av datacenter som representerar Azure tillgänglighet zoner är en kompromiss mellan leverera Nätverksfördröjningen mellan tjänster som distribueras i olika zoner som accepteras för de flesta program och ett visst avstånd mellan datacenter. Så att katastrofer helst inte påverkar power och leverans av nätverk och infrastruktur för alla zoner för tillgänglighet i den här regionen. Dock som monument katastrofer visade tillgänglighet zoner alltid inte eventuellt att ange tillgängligheten i en region som du vill. Tänk på att orkan Maria som uppnått dataö registreringen på 2017-08/20 och i princip orsakade ett nära 100% svart ut på 90 miles wide ö.   
  


## <a name="single-vm-scenario"></a>Enskild VM-scenario
I det här scenariot har du skapat ett Azure virtuell dator för SAP HANA-instansen. Du har använt Azure Premium Storage värd för operativsystemets disk och alla datadiskar. Drifttid-SLA för 99,9% av Azure och serviceavtal för andra Azure-komponenter är tillräcklig att uppfylla dina tillgänglighets-SLA för dina kunder. I det här scenariot behöver du inte använda en Azure Tillgänglighetsuppsättning för virtuella datorer som kör DBMS-lagret. I det här scenariot du förlita dig på två olika funktioner:

- Azure VM automatiskt starta om (även kallad Azure Service återställning)
- SAP HANA automatisk omstart

Azure VM automatiskt starta om eller tjänsten återställning är en funktion i Azure som fungerar på två nivåer:

- Azure-servervärd kontrollerar hälsotillståndet för en virtuell dator som finns på server-värd
- Azure-Infrastrukturkontrollanten övervakning av hälsotillstånd och tillgänglighet för server-värd

För varje VM som finns på en server i Azure-värd, övervakar en health check-funktionalitet hälsotillståndet för värdbaserade virtuella datorer. Om virtuella datorer som hör till ett icke hälsosamma tillstånd, kan en omstart av den virtuella datorn initieras av agenten för Azure-värd som kontrollerar hälsotillståndet för den virtuella datorn. Azure-Infrastrukturkontrollanten kontrollerar hälsotillståndet för värden genom att kontrollera många olika parametrar som indikerar problem med värdmaskinvaran, men kontrollerar också på tillgängligheten för värd via nätverket. Ett tecken på problem med-värden kan leda till åtgärder som:

- Starta om datorn på värden och omstart av virtuella datorer som körs på värden om värden signalerar ett felaktigt hälsotillstånd
- Omstart av värden och omstart av den virtuella datorer som fanns från början på värden på en felfri värd om värden inte är i felfritt tillstånd efter omstart. Värden är i det här fallet kommer att vara markeras som ej felfri och används inte för ytterligare distributioner förrän avmarkerad eller ersatts.
- Omedelbar omstart av virtuella datorer på en felfri värd i fall där felaktiga värden har problem i processen för omstart. 

Med värden och Virtuella övervakning som tillhandahålls av Azure, Azure virtuella datorer som råkar ut för värdproblem med automatiskt startas om på en felfri Azure-värd 

Funktionen andra du förlita dig på i ett sådant scenario är att din HANA-tjänst som körs inom en startas om VM startas automatiskt efter omstart av den virtuella datorn. Den [HANA tjänsten automatisk omstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) kan konfigureras via tjänsten watchdog olika HANA-tjänster.

Detta enda VM-scenario kan få bättre genom att lägga till en kalla redundansnod en SAP HANA-konfiguration. Eller som framhävs i SAP HANA-dokumentationen som [värden automatisk växling vid fel](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Den här konfigurationen kan begripliga i lokala distributionen situationer där servermaskinvaran är begränsat och du dedikerar en enskild server-nod som värd automatisk växling vid fel nod för en uppsättning produktionsvärdar. Men situationer som Azure där den underliggande infrastrukturen Azure tillhandahåller en felfri målserver för en lyckad omstart av en virtuell dator SAP HANA värden automatisk växling scenariot inte vara klokt att distribuera. 

Därför har vi inga referensarkitektur som förutser en Standby-nod för HANA värden automatiskt-redundans. Detta gäller även för SAP HANA skalbar konfigurationer.


## <a name="availability-scenarios-involving-two-different-vms"></a>Tillgänglighet scenarier som inbegriper två olika virtuella datorer
Med två virtuella Azure-datorer i Azure-Tillgänglighetsuppsättningar kan du öka drifttid mellan dessa två virtuella datorer om dessa virtuella datorer placeras i en Azure Tillgänglighetsuppsättning i en Azure-region. Grundinstallationen i Azure som ser ut som grafik som visas här: ![två virtuella datorer med alla lager](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

För att illustrera olika tillgänglighet scenarier är några av ovanstående lagren klipp ut och grafik begränsat till lager av virtuella datorer, värdar, Tillgänglighetsuppsättningar och Azure-regioner. Azure Vnet, resursgrupper och prenumerationer spelar inte roll för scenarier som beskrivs.

### <a name="replicating-backups-to-second-virtual-machine"></a>Replikera säkerhetskopieringar till andra virtuell dator
En av de mest elementära inställningarna är att ha säkerhetskopior, särskilt säkerhetskopieringar av transaktionsloggen levereras från en virtuell dator till en annan Azure virtuell dator. Du kan välja mellan alla Azure Storage-typer. Du är ansvarig för skriptkörning kopia av schemalagda säkerhetskopieringar som utförts på den första virtuella datorn till den andra virtuella datorn. Vid använder kräver instanser av den andra virtuella datorn, skulle du behöver återställa den fullständiga och inkrementella/differentiella säkerhetskopieringar av transaktionsloggen till den punkt som du behöver. Arkitekturen skulle se ut: ![två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Den här installationen är inte lämpligt för för att uppnå bra Återställningspunktmål och Återställningstidsmål gånger. Särskilt RTO gånger bli lidande på grund av behovet av att fullständigt återställa fullständig databasen med de kopierade säkerhetskopieringarna. Men den här konfigurationen är användbar för att återställa från oavsiktlig borttagning för centrala-instanser. Extrahera data med dessa inställningar du kan när som helst för att återställa till en viss punkt i tiden, och importera den borttagna data till din huvudsakliga instans. Därför kan det vara klokt att använda denna säkerhetskopia metoden tillsammans med andra funktioner för hög tillgänglighet. Under den tid då bara säkerhetskopiorna kopieras kan du få tillsammans med en mindre VM än den huvudsakliga SAP HANA instanser körs i. Men kom ihåg att mindre virtuella datorer har lägre antal virtuella hårddiskar som kan bifogas. Kontrollera [storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) för gränserna för enskilda VM-typer.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>Med hjälp av replikering för SAP HANA-System utan automatisk redundans
I följande scenarier använder du replikering för SAP HANA-systemet. SAP utfärdat dokumentationen hittar du från och med artikeln [System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Det finns två olika konfigurationer som har vissa skillnader i de mål mellan två virtuella Azure-datorer i en enda Azure-region. I allmänhet kanske scenarier med inte med automatisk redundans inte för relevant för scenarier inom en Azure-region. För som beror på att fel oftast i Azure-infrastrukturen Azure Service återställning kommer att starta om den primära virtuella datorn på en annan värd. Det finns endast tillfällen kant där en sådan konfiguration kan hjälpa vad gäller scenarier. Eller tillfällen du som kund vill upptäcker, särskilt runt effektivitet.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Med HANA System Replication utan automatisk redundans och utan att läsa in data 
Det här är ett scenario där du använder SAP HANA System replikering för att flytta data på en synkron sätt för att uppnå en återställning punkt mål (RPO) 0. På den andra sidan som du har en tillräckligt lång Recovery tid mål för Återställningstid, så som du inte behöver redundans eller läsa in data till HANA instans-cachen. I sådana fall har möjligheter att driva ekonomi i konfigurationen av:

- Du kan köra en annan SAP HANA-instans i den andra virtuella datorn som tar de flesta av minne på den virtuella datorn. Denna instans är vanligtvis en instans som vid en redundansväxling till den andra virtuella datorn kunde stängas av. Så som den replikerade data kan läsas in i cacheminnet för den avsedda HANA databasinstansen i det andra Virtuellt.
- Du kan använda en mindre VM-storlek som den andra virtuella datorn. Vid redundans har du ett steg innan manuell växling vid fel där du vill ändra storlek så att storleken på den Virtuella källdatorn. Det ser ut så scenariot:

![Två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Även om du inte före datainläsning i HANA System replikeringsmål, behöver du minst 64 GB minne och utöver det tillräckligt med minnet för att skydda rowstore data i minnet på målinstansen.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>Med HANA System Replication utan automatisk redundans och läsa in data
Scenariot införts före skillnaden är att data som replikeras till HANA-instans i den andra virtuella datorn redan har lästs in. Detta minskar två fördelar som du kan ha med scenario inte före inläsning av data. I detta fall kan köra du inte ett annat SAP HANA-system på den andra virtuella datorn. Eller så kan du använda en mindre VM-storlek. Därför är det ett scenario som implementerats knappt med kunder


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Med automatisk redundans SAP HANA System replikering

Konfiguration standard tillgänglighet i ett Azure-region merparten av kunderna implementera med SAP HANA, är en konfiguration där två Azure virtuella datorer som kör Linux SLES har ett failover-kluster som har definierats. Linux-kluster med SLES baseras på den [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework tillsammans med en [STONITH](http://linux-ha.org/wiki/STONITH) enhet. Replikeringsläge används synkroniseras från en SAP HANA-perspektiv och en automatisk redundans har konfigurerats. I det andra Virtuellt SAP HANA-instans som fungerar som en hot standby-nod som tar emot en synkron dataström ändra poster från den primära SAP HANA-instansen. Eftersom transaktioner hämta allokerat av programmet på den primära noden HANA väntar den primära noden HANA bekräfta genomförande till programmet förrän den sekundära noden för SAP HANA bekräftas mottagandet av commit-posten. Lägen för SAP HANA två olika synkron replikering. Läs artikeln för information och skillnader på synkron replikering lägena [replikering lägen för SAP HANA System replikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)

Övergripande konfigurationen ser ut

![Två virtuella datorer med storage-replikering och redundans](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Den här lösningen är valt eftersom det gör det möjligt att uppnå ett Återställningpunktsmål = 0 och ett mycket låga RTO gånger. Konfigurera anslutningen för SAP HANA-klienter på ett sätt som SAP HANA-klienter använder den virtuella IP-adressen för att ansluta till HANA System replikeringskonfiguration. Detta eliminerar måste konfigurera om programmet vid redundans till den sekundära noden. I den här lösningen måste Azure VM SKU: er för primärt eller sekundära vara samma.  


## <a name="next-steps"></a>Nästa steg
Om du behöver steg-för-steg-anvisningar om hur du ställer in en sådan konfiguration i Azure läser du i artiklar:

- [Konfigurera SAP HANA System Replication i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Din SAP på Azure – del 4 – hög tillgänglighet för SAP HANA med System-replikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Om du behöver mer information om tillgänglighet för SAP HANA över Azure-regioner kan läsa:

- [Tillgänglighet för SAP HANA över Azure-regioner](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

