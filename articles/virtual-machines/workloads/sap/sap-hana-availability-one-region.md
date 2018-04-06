---
title: SAP HANA tillgänglighet inom en Azure-region | Microsoft Docs
description: Beskriver SAP HANA-åtgärder på Azure interna virtuella datorer i en Azure-region.
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
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b397d03cbb8fc539eb5f79e183233515bf173a99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA tillgänglighet inom en Azure-region
Den här artikeln beskriver flera scenarier för tillgänglighet inom en Azure-region. Azure har många regioner utspridda över hela världen. Lista över Azure-regioner, se [Azure-regioner](https://azure.microsoft.com/regions/). Microsoft erbjuder distribution av en enskild virtuell dator med en HANA-instans för att distribuera SAP HANA på virtuella datorer i en Azure-region. För ökad tillgänglighet kan du distribuera två virtuella datorer med två HANA instanser inom en [Azure tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) som använder HANA system replikering för tillgänglighet. 

För närvarande Azure erbjuder en förhandsversion av [Azure tillgänglighet zoner (förhandsgranskning)](https://docs.microsoft.com/azure/availability-zones/az-overview). I den här artikeln beskriver vi inte tillgänglighet zoner i detalj. Men vi ta med en allmän diskussion om med hjälp av tillgänglighetsuppsättningar jämfört med tillgänglighet zoner.

Vad är skillnaden mellan en tillgänglighetsuppsättning och en zon för tillgänglighet i Azure? Azure-regioner där tillgänglighet zoner erbjuds har flera datacenter. Datacenter är oberoende leverans av kraftkälla kylning och nätverk. Det beror på för att erbjuda olika zoner inom ett enda Azure-region så att du kan distribuera program i två eller tre tillgänglighet zoner som erbjuds. Förutsatt att power käll- eller problem påverkar endast en infrastruktur för tillgänglighet zonen, fungerar programdistributionen i en Azure-region fortfarande om du använder tillgänglighet zoner. Vissa minskad kapacitet kan uppstå. Till exempel virtuella datorer i en zon kan gå förlorade, men virtuella datorer i de två zonerna är igång. 
 
En Azure tillgänglighetsuppsättning är en logisk gruppering funktion som hjälper dig att se till att VM-resurser som du placerar i tillgänglighetsuppsättningen är fel isoleras från varandra när de distribueras i ett Azure-datacenter. Azure säkerställer att de virtuella datorerna du placera inom en tillgänglighet in kör över flera fysiska servrar, beräkning rack, lagringsenheter och nätverksväxlar. I vissa Azure dokumentationen för den här konfigurationen kallas placeringar i olika [uppdatering och feltolerans domäner](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Dessa placeringar är vanligen inom ett Azure-datacenter. Förutsatt att power käll- och problem kan påverka det datacenter som du distribuerar, påverkas alla kapacitet i en Azure-region.

Placeringen av datacenter som representerar Azure tillgänglighet zoner är en kompromiss mellan leverera Nätverksfördröjningen mellan tjänster som distribueras i olika zoner som är acceptabel för de flesta program och ett visst avstånd mellan datacenter. Katastrofer skulle helst påverkar ström, nätverk tillgång och infrastruktur för alla zoner för tillgänglighet i den här regionen. Dock monument katastrofer har visas kanske tillgänglighet zoner inte alltid tillhandahålla tillgänglighet som du vill använda i en region. Tänk på att orkan Maria som träffar dataö registreringen på 20 September 2017. Orkanen orsakade i princip en nästan 100 procent blackout på 90 mil hela ö.

## <a name="single-vm-scenario"></a>Enskild VM-scenario

I ett enda VM-scenario skapar du en Azure VM för SAP HANA-instansen. Du kan använda Azure Premium-lagring för värd för operativsystemets disk och alla datadiskar. Azure drifttid SLA för 99,9 procent och serviceavtal för andra Azure-komponenter är tillräcklig att uppfylla dina tillgänglighets-SLA för dina kunder. I det här scenariot behöver du inte använda en Azure tillgänglighetsuppsättning för virtuella datorer som kör DBMS-lagret. I det här scenariot kan du förlita dig på två olika funktioner:

- Azure VM automatisk omstart (kallas även återställning av Azure-tjänst)
- SAP HANA automatisk omstart

Azure VM automatisk omstart eller tjänsten återställning är en funktion i Azure som fungerar på två nivåer:

- Azure servervärden kontrollerar hälsotillståndet för en virtuell dator som finns på server-värden.
- Azure-infrastrukturkontrollanten övervakar hälsotillstånd och tillgänglighet för server-värd.

En health check-funktionalitet övervakar hälsotillståndet för varje virtuell dator som finns på en server i Azure-värd. Om en virtuell dator hamnar i ett nonhealthy tillstånd, kan en omstart av den virtuella datorn initieras av agenten för Azure-värd som kontrollerar hälsotillståndet för den virtuella datorn. I infrastrukturkontrollanten kontrollerar hälsotillståndet för värden genom att kontrollera många olika parametrar som kan tyda på problem med värdens maskinvara. Den kontrollerar också på tillgängligheten för värd via nätverket. Ett tecken på problem med-värden kan leda till följande händelser:

- Om värden signalerar ett felaktigt hälsotillstånd, en omstart av värden och en omstart av virtuella datorer som körs på värden.
- Om värden inte är i felfritt tillstånd efter omstarten, en omstart av värden och startar om de virtuella datorer som fanns från början på värden på en felfri värd. I det här fallet markeras värden som ohälsosam. Den används inte för ytterligare distributioner förrän den har rensats eller ersatts.
- Om felaktiga värden får problem under omstart är en omedelbar starta om på de virtuella datorerna på en felfri värd. 

Med värden och Virtuella övervakning som tillhandahålls av Azure, Azure virtuella datorer som har värdproblem automatiskt startas om på en felfri Azure-värd. 

Andra funktionen som förlitar sig på i det här fallet är det faktum att HANA-tjänsten som körs i en startas om virtuell dator startas automatiskt efter den virtuella datorn startas om. Du kan ställa in [HANA tjänsten automatisk omstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) via tjänsten watchdog för olika HANA tjänster.

Du kan förbättra detta enda VM-scenario genom att lägga till en kalla redundansnod en SAP HANA-konfiguration. Den här inställningen kallas i dokumentationen för SAP HANA [värd för automatisk redundans](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Den här konfigurationen kan begripliga i en lokal distribution situation där servermaskinvaran är begränsad, och du dedikerar en enskild server-nod som värd för automatisk redundans nod för en uppsättning produktionsvärdar. Men i Azure, där den underliggande infrastrukturen Azure tillhandahåller en felfri målserver för en lyckad VM-omstart, det vara inte klokt att distribuera SAP HANA värden automatiskt-redundans. Därför har vi inga referensarkitektur som förutser en vänteläge nod för HANA värden automatiskt-redundans. Detta gäller även för SAP HANA skalbar konfigurationer.

## <a name="availability-scenarios-for-two-different-vms"></a>Tillgänglighet scenarier för två olika virtuella datorer

Om du använder två virtuella Azure-datorer i tillgänglighetsuppsättningen Azure kan du öka drifttid mellan dessa två virtuella datorer om de är placerade i en Azure tillgänglighetsuppsättning i en Azure-region. Grundinstallationen i Azure skulle se ut så här:

![Diagram över två virtuella datorer med alla lager](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

För att illustrera olika tillgänglighet scenarier utelämnas några lager i diagrammet. Diagrammet visar endast lager som beskriver virtuella datorer, värdar, tillgänglighetsuppsättningar och Azure-regioner. Azure Virtual Network-instanser, resursgrupper och prenumerationer ha inte en roll i scenarier som beskrivs i det här avsnittet.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikera säkerhetskopieringar till en andra virtuell dator

En av de mest elementära inställningarna är att använda säkerhetskopior. I synnerhet kanske säkerhetskopieringar av transaktionsloggen levereras från en virtuell dator till en annan virtuell dator i Azure. Du kan välja vilken Azure Storage. Du är ansvarig för skriptkörning kopia av schemalagda säkerhetskopieringar som utförs på den första virtuella datorn till den andra virtuella datorn i den här installationen. Om du behöver använda andra VM-instanser måste du återställa den fullständiga och inkrementella/differentiella säkerhetskopieringar av transaktionsloggen så att du behöver. 

Arkitekturen ser ut så här:

![Diagram över två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Den här installationen är inte bra att uppnå bra Recovery punkt mål (RPO) och återställning tid mål för Återställningstid gånger. Återställningstidsmålet gånger bli särskilt lidande på grund av att behöva återställa fullständig databasen med hjälp av de kopierade säkerhetskopieringarna. Den här konfigurationen är användbar för att återställa från oavsiktlig borttagning för centrala-instanser. Med den här inställningen när som helst du återställa till en viss punkt i tiden, extrahera data och importera den borttagna data till din huvudsakliga instans. Därför kan vara det bra att använda en säkerhetskopia metod tillsammans med andra funktioner för hög tillgänglighet. 

Medan säkerhetskopiorna kopieras kanske du kan använda en mindre VM än den huvudsakliga VM som SAP HANA-instansen körs på. Tänk på att du kan koppla ett mindre antal virtuella hårddiskar till mindre virtuella datorer. Information om gränserna för enskilda VM-typer finns [storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>Replikering för SAP HANA-system utan automatisk redundans

Scenarier som beskrivs i det här avsnittet använda replikering för SAP HANA-system. SAP-dokumentation finns [System replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Två virtuella Azure-datorer i en enda Azure-region har olika konfigurationer, så att det finns vissa skillnader i Återställningstidsmål. I allmänhet kanske scenarier utan automatisk redundans inte gäller specifikt för virtuella datorer i en Azure-region. Det beror på att Azure-tjänsten återställning startas om den primära virtuella datorn på en annan värd för de flesta fel i Azure-infrastrukturen. Det finns tillfällen kant där den här konfigurationen kan hjälpa vad gäller scenarier. Eller, i vissa fall kan en kund kanske vill utnyttja mer effektivt.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA system replikering utan automatisk redundans och utan data preload

I det här scenariot använder du SAP HANA system replikering för att flytta data på en synkron sätt att uppnå ett Återställningpunktsmål 0. Å andra sidan kan du ha en tillräckligt lång RTO som du inte behöver redundans eller data som läses in i förväg till HANA instans-cachen. I det här fallet är det möjligt att uppnå ekonomi konfigurationen genom att utföra följande åtgärder:

- Kör en annan SAP HANA-instans i den andra virtuella datorn. SAP HANA-instans i den andra virtuella datorn tar de flesta av minne på den virtuella datorn. Detta är vanligen om uppstår redundans för andra VM. Du kan stänga av den andra virtuella datorn så att den replikerade data kan läsas in i cacheminnet för den avsedda HANA databasinstansen i det andra Virtuellt.
- Använda en mindre VM-storlek på den andra virtuella datorn. Om det uppstår redundans, har du ytterligare ett steg innan manuell växling vid fel. I det här steget kan du ändra storlek så att storleken på den Virtuella källdatorn. Scenariot ser ut så här:

![Diagram över två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Även om du inte använder data preload i replikeringsmålet HANA system, behöver du minst 64 GB minne. Du måste också tillräckligt med minne förutom 64 GB för att lagra rowstore-data i minnet på målinstansen.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA system replikering utan automatisk redundans och med preload-data

I det här scenariot är data som replikeras till HANA-instansen på den andra Virtuellt i förväg. Detta eliminerar två fördelarna med förinstalleras data. I detta fall kan köra du inte ett annat SAP HANA-system på den andra virtuella datorn. Du kan också använda en mindre VM-storlek. Därför måste implementera kunder sällan det här scenariot.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>Replikering för SAP HANA-system med automatisk redundans

I standarden och de vanligaste tillgänglighetskonfiguration inom en Azure-regionen två virtuella Azure-datorer kör Linux SLES har ett redundanskluster som definierats. SLES Linux-kluster är baserad på den [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework, tillsammans med en [STONITH](http://linux-ha.org/wiki/STONITH) enhet. 

Replikeringsläge som används är synkroniserad från en SAP HANA-perspektiv och en automatisk redundans har konfigurerats. I det andra Virtuellt SAP HANA-instans som fungerar som en nod i varmt vänteläge. Noden vänteläge tar emot en synkron dataström ändra poster från den primära SAP HANA-instansen. Eftersom transaktioner har utförts av programmet på den primära noden HANA väntar den primära noden HANA bekräfta genomförande till programmet tills den sekundära noden för SAP HANA bekräftar den emot commit-posten. Det finns två synkron replikering lägen för SAP HANA. Mer information och en beskrivning av skillnaderna mellan dessa två lägen för synkron replikering, se SAP-artikel [replikering lägen för SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Övergripande konfigurationen ser ut så här:

![Diagram över två virtuella datorer med storage-replikering och redundans](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Du kan välja den här lösningen eftersom den gör det möjligt att uppnå ett Återställningpunktsmål = 0 och ett mycket låga RTO. Konfigurera anslutningen för SAP HANA-klienter så att SAP HANA-klienter använder den virtuella IP-adressen för att ansluta till HANA replikering systemkonfigurationen. Detta eliminerar behovet av att konfigurera om programmet om det uppstår redundans till den sekundära noden. I det här scenariot måste Azure VM SKU: er för de primära och sekundära virtuella datorerna vara samma.

## <a name="next-steps"></a>Nästa steg

Stegvisa anvisningar om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera replikering för SAP HANA-system i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av system-replikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

För mer information om tillgänglighet för SAP HANA över Azure-regioner, se:

- [Tillgänglighet för SAP HANA över Azure-regioner](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

