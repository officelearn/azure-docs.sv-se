---
title: SAP HANA-tillgänglighet inom en Azure-region | Microsoft-dokument
description: Beskriver SAP HANA-åtgärder på inbyggda virtuella Azure-datorer i en Azure-region.
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
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef7161e653ec582708f242b67c643d960d75e27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255460"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA-tillgänglighet inom en Azure-region
I den här artikeln beskrivs flera tillgänglighetsscenarier inom en Azure-region. Azure har många regioner, spridda över hela världen. Information om listan över Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/). För distribution av SAP HANA på virtuella datorer inom en Azure-region erbjuder Microsoft distribution av en enda virtuell dator med en HANA-instans. För ökad tillgänglighet kan du distribuera två virtuella datorer med två HANA-instanser i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) som använder HANA-systemreplikering för tillgänglighet. 

Azure erbjuder för närvarande [Azure-tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). I den här artikeln beskrivs inte tillgänglighetszoner i detalj. Men den innehåller en allmän diskussion om hur du använder tillgänglighetsuppsättningar kontra tillgänglighetszoner.

Azure-regioner där tillgänglighetszoner erbjuds har flera datacenter. Datacenter är oberoende i strömförsörjningen, kylning och nätverk. Anledningen till att erbjuda olika zoner inom en enda Azure-region är att distribuera program över två eller tre tillgänglighetszoner som erbjuds. Distribuera över zoner, problem i kraft och nätverk som påverkar endast en Azure Availability Zone-infrastruktur, är din programdistribution inom en Azure-region fortfarande funktionell. Viss nedsatt kapacitet kan förekomma. Till exempel kan virtuella datorer i en zon gå förlorade, men virtuella datorer i de andra två zonerna skulle fortfarande vara igång. 
 
En Azure-tillgänglighetsuppsättning är en logisk grupperingsfunktion som säkerställer att de vm-resurser som du placerar inom tillgänglighetsuppsättningen är felinsolerade från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. I viss Azure-dokumentation kallas den här konfigurationen för placeringar i olika [uppdaterings- och feldomäner](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Dessa placeringar är vanligtvis inom ett Azure-datacenter. Om du antar att energikälla och nätverksproblem skulle påverka det datacenter som du distribuerar, påverkas all din kapacitet i en Azure-region.

Placeringen av datacenter som representerar Azure-tillgänglighetszoner är en kompromiss mellan att leverera acceptabel nätverksfördröjning mellan tjänster som distribueras i olika zoner och ett avstånd mellan datacenter. Naturkatastrofer skulle helst inte påverka kraft, nätförsörjning och infrastruktur för alla tillgänglighetszoner i den här regionen. Men som monumentala naturkatastrofer har visat, tillgänglighetszoner kanske inte alltid ger den tillgänglighet som du vill ha inom en region. Tänk på orkanen Maria som drabbade ön Puerto Rico den 20 september 2017. Orkanen orsakade i princip en nästan 100 procent strömavbrott på 90 mil bred ö.

## <a name="single-vm-scenario"></a>Scenario med en virtuell dator

I ett scenario med en virtuell dator skapar du en Virtuell Azure-dator för SAP HANA-instansen. Du använder Azure Premium Storage som värd för operativsystemdisken och alla dina datadiskar. Azure uptime SLA på 99,9 procent och SLA för andra Azure-komponenter är tillräckligt för att du ska kunna uppfylla dina tillgänglighets-SLA:er för dina kunder. I det här fallet behöver du inte utnyttja en Azure-tillgänglighetsuppsättning för virtuella datorer som kör DBMS-lagret. I det här fallet förlitar du dig på två olika funktioner:

- Azure VM automatisk omstart (kallas även Azure-tjänst healing)
- SAP HANA startar om automatiskt

Automatisk omstart av Azure VM eller tjänstläkning är en funktion i Azure som fungerar på två nivåer:

- Azure-servervärden kontrollerar hälsotillståndet för en virtuell dator som finns på servervärden.
- Azure-infrastrukturstyrenheten övervakar servervärdens hälsa och tillgänglighet.

En hälsokontrollfunktion övervakar hälsotillståndet för alla virtuella datorer som finns på en Azure-servervärd. Om en virtuell dator faller i ett icke-felfritt tillstånd kan en omstart av den virtuella datorn initieras av Azure-värdagenten som kontrollerar hälsotillståndet för den virtuella datorn. Fabric-styrenheten kontrollerar värdens hälsa genom att kontrollera många olika parametrar som kan tyda på problem med värdmaskinvaran. Den kontrollerar också värdens tillgänglighet via nätverket. En indikation på problem med värden kan leda till följande händelser:

- Om värden signalerar ett dåligt hälsotillstånd utlöses en omstart av värden och en omstart av de virtuella datorer som kördes på värden.
- Om värden inte är i felfritt tillstånd efter en lyckad omstart initieras en omfördelning av de virtuella datorer som ursprungligen fanns på den nu felaktiga noden till en felfri värdserver. I det här fallet markeras den ursprungliga värden som inte felfri. Den används inte för ytterligare distributioner förrän den har rensats eller ersatts.
- Om den felaktiga värden har problem under omstartsprocessen utlöses en omedelbar omstart av de virtuella datorerna på en felfri värd. 

Med värd- och VM-övervakningen från Azure startas virtuella Azure-virtuella datorer som får problem med värdupplevelsen automatiskt om på en felfri Azure-värd. 

>[!IMPORTANT]
>Azure-tjänstläkning startar inte linux virtuella datorer där gästoperativsystemet är i kernel-paniktillstånd. Standardinställningarna för de vanliga Linux-utgåvorna startar inte automatiskt om virtuella datorer eller servrar där Linux-kärnan är i paniktillstånd. Istället standard förutser att hålla OS i kärnan panik tillstånd för att kunna bifoga en kärna felsökare att analysera. Azure hedrar det beteendet genom att inte automatiskt starta om en virtuell dator med gästoperativsystemet i ett sådant tillstånd. Antagandet är att sådana händelser är extremt sällsynta. Du kan skriva över standardbeteendet för att aktivera en omstart av den virtuella datorn. Om du vill ändra standardbeteendet aktiverar du parametern kernel.panic i /etc/sysctl.conf. Den tid du anger för den här parametern är i sekunder. Vanliga rekommenderade värden är att vänta i 20-30 sekunder innan du utlöser omstarten via den här parametern. Se <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>även .

Den andra funktionen som du förlitar dig på i det här scenariot är det faktum att HANA-tjänsten som körs i en startad virtuell dator startar automatiskt efter att den virtuella datorn har startats om. Du kan ställa in [HANA-tjänsten automatisk omstart via watchdog-tjänsterna](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) för de olika HANA-tjänsterna.

Du kan förbättra det här scenariot med en virtuell dator genom att lägga till en kall redundannod i en SAP HANA-konfiguration. I SAP HANA-dokumentationen kallas den här inställningen [för automatisk redundans värd.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html) Den här konfigurationen kan vara meningsfull i en lokal distributionssituation där servermaskinvaran är begränsad och du avsätter en nod för en server som värdnod för automatisk redundansnod för en uppsättning produktionsvärdar. Men i Azure, där den underliggande infrastrukturen i Azure tillhandahåller en felfri målserver för en lyckad omstart av den virtuella datorn, är det inte meningsfullt att distribuera SAP HANA-värd för automatisk redundans. På grund av azure-tjänstläkning finns det ingen referensarkitektur som förutser en standby-nod för HANA-värd automatisk redundans. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Specialfall för SAP HANA-utskalningskonfigurationer i Azure
Hög tillgänglighet för SAP HANA-utskalningskonfigurationer är beroende av tjänstläkning av virtuella Azure-datorer och omstart av SAP HANA-instansen när den virtuella datorn är igång igen. Arkitekturer med hög tillgänglighet baserat på HANA System Replication kommer att introduceras vid ett senare tillfälle. 


## <a name="availability-scenarios-for-two-different-vms"></a>Tillgänglighetsscenarier för två olika virtuella datorer

Om du använder två virtuella Azure-datorer i en Azure-tillgänglighetsuppsättning kan du öka drifttiden mellan dessa två virtuella datorer om de placeras i en Azure-tillgänglighetsuppsättning inom en Azure-region. Basinställningarna i Azure skulle se ut så här:

![Diagram över två virtuella datorer med alla lager](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

För att illustrera de olika tillgänglighetsscenarierna utelämnas några av lagren i diagrammet. Diagrammet visar bara lager som visar virtuella datorer, värdar, tillgänglighetsuppsättningar och Azure-regioner. Azure Virtual Network-instanser, resursgrupper och prenumerationer spelar ingen roll i de scenarier som beskrivs i det här avsnittet.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikera säkerhetskopior till en andra virtuell dator

En av de mest rudimentära inställningar är att använda säkerhetskopior. I synnerhet kan du ha transaktionsloggsäkerhetskopior som levereras från en virtuell dator till en annan Azure VM. Du kan välja typ av Azure Storage. I den här inställningen är du ansvarig för skriptning av kopian av schemalagda säkerhetskopior som utförs på den första virtuella datorn till den andra virtuella datorn. Om du behöver använda de andra VM-instanserna måste du återställa alla, inkrementella/differentierings- och transaktionsloggsäkerheter till den punkt som du behöver. 

Arkitekturen ser ut som:

![Diagram över två virtuella datorer med lagringsreplikering](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Den här inställningen är inte väl lämpad för att uppnå stora recovery point-mål (RPO) och recovery time objective (RTO) gånger. RTO gånger särskilt skulle drabbas på grund av behovet av att helt återställa hela databasen med hjälp av kopierade säkerhetskopior. Den här inställningen är dock användbar för återställning från oavsiktlig dataradering på huvudinstanserna. Med den här inställningen kan du när som helst återställa till en viss tidpunkt, extrahera data och importera borttagna data till huvudinstansen. Därför kan det vara meningsfullt att använda en metod för säkerhetskopieringskopiering i kombination med andra funktioner med hög tillgänglighet. 

Medan säkerhetskopior kopieras kanske du kan använda en mindre virtuell dator än huvuddatorn för den virtuella datorn som SAP HANA-instansen körs på. Tänk på att du kan koppla ett mindre antal virtuella hårddiskar till mindre virtuella datorer. Information om gränserna för enskilda VM-typer finns i [Storlekar för virtuella Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA-systemreplikering utan automatisk redundans

De scenarier som beskrivs i det här avsnittet använder SAP HANA-systemreplikering. Dokumentation för SAP finns i [Systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenarier utan automatisk redundans är inte vanliga för konfigurationer inom en Azure-region. En konfiguration utan automatisk redundans, men undviker en Pacemaker-konfiguration, tvingar dig att övervaka och redundans manuellt. Eftersom detta tar och ansträngningarna också, de flesta kunder är beroende av Azure-tjänsten healing istället. Det finns vissa kantfall där den här konfigurationen kan vara till hjälp när det gäller felscenarier. Eller, i vissa fall, en kund kanske vill inse mer effektivitet.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA-systemreplikering utan automatisk redundans och utan förinläsning av data

I det här fallet använder du SAP HANA-systemreplikering för att flytta data på ett synkront sätt för att uppnå en RPO på 0. Å andra sidan har du en tillräckligt lång RTO som du inte behöver antingen redundans eller data förinläsning i HANA-instanscachen. I det här fallet är det möjligt att uppnå ytterligare ekonomi i konfigurationen genom att vidta följande åtgärder:

- Kör en annan SAP HANA-instans i den andra virtuella datorn. SAP HANA-instansen i den andra virtuella datorn tar det mesta av minnet på den virtuella datorn. Om en redundans till den andra virtuella datorn måste du stänga av den SAP HANA-instans som körs som har data som är helt inlästa i den andra virtuella datorn, så att replikerade data kan läsas in i cacheminnet för den riktade HANA-instansen i den andra virtuella datorn.
- Använd en mindre vm-storlek på den andra virtuella datorn. Om en redundans inträffar har du ytterligare ett steg innan den manuella redundansen. I det här steget ändrar du storlek på den virtuella datorn till storleken på källdatorn. 
 
Scenariot ser ut som:

![Diagram över två virtuella datorer med lagringsreplikering](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Även om du inte använder dataförinläsning i HANA-systemets replikeringsmål behöver du minst 64 GB minne. Du behöver också tillräckligt med minne utöver 64 GB för att hålla rowstore-data i minnet för målinstansen.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA-systemreplikering utan automatisk redundans och med dataförspänning

I det här fallet är data som replikeras till HANA-instansen i den andra virtuella datorn förinläst. Detta eliminerar de två fördelarna med att inte förinstallera data. I det här fallet kan du inte köra ett annat SAP HANA-system på den andra virtuella datorn. Du kan inte heller använda en mindre vm-storlek. Därför implementerar kunder sällan det här scenariot.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA-systemreplikering med automatisk redundans

I standardkonfigurationen och den vanligaste tillgänglighetskonfigurationen inom en Azure-region har två virtuella Azure-datorer som kör SLES Linux ett redundanskluster definierat. SLES Linux-klustret [Pacemaker](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) är baserat på Pacemaker-ramverket, tillsammans med en [STONITH-enhet.](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) 

Ur ett SAP HANA-perspektiv synkroniseras replikeringsläget som används och en automatisk redundans konfigureras. I den andra virtuella datorn fungerar SAP HANA-instansen som en instans med aktiv vänteläge. Standby-noden tar emot en synkron ström av ändringsposter från den primära SAP HANA-instansen. När transaktioner har genomförts av programmet vid HANA-primärnoden väntar den primära HANA-noden för att bekräfta åtagandet till programmet tills den sekundära SAP HANA-noden bekräftar att den tog emot commit-posten. SAP HANA erbjuder två synkrona replikeringslägen. Mer information och en beskrivning av skillnaderna mellan dessa två synkrona replikeringslägen finns i SAP-artikeln [Replikeringslägen för SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Den övergripande konfigurationen ser ut som:

![Diagram över två virtuella datorer med lagringsreplikering och redundans](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Du kan välja den här lösningen eftersom den gör att du kan uppnå en RPO=0 och en låg RTO. Konfigurera SAP HANA-klientanslutningen så att SAP HANA-klienterna använder den virtuella IP-adressen för att ansluta till HANA-systemreplikeringskonfigurationen. En sådan konfiguration eliminerar behovet av att konfigurera om programmet om en redundans till den sekundära noden inträffar. I det här fallet måste Azure VM SKU:er för de primära och sekundära virtuella datorerna vara desamma.

## <a name="next-steps"></a>Nästa steg

Stegvis vägledning om hur du konfigurerar dessa konfigurationer i Azure finns i:

- [Konfigurera SAP HANA-systemreplikering i virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Mer information om SAP HANA-tillgänglighet i Azure-regioner finns i:

- [SAP HANA-tillgänglighet i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

