---
title: SAP HANA-tillgänglighet inom en Azure-region | Microsoft Docs
description: Beskriver åtgärder för SAP HANA på Azure interna virtuella datorer i en Azure-region.
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
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 687012e73b4b0c869b491ac1c9ea128662b23510
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60391505"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA-tillgänglighet inom en Azure-region
Den här artikeln beskriver flera scenarier för tillgänglighet inom en Azure-region. Azure har många regioner och sprider sig över hela världen. Lista över Azure-regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/). Microsoft erbjuder för att distribuera SAP HANA på virtuella datorer i en Azure-region, distribution av en enskild virtuell dator med en HANA-instans. För ökad tillgänglighet kan du distribuera två virtuella datorer med två HANA-instanser i en [Azure-tillgänglighetsuppsättning](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) som använder HANA-systemreplikering för tillgänglighet. 

För närvarande Azure erbjuder [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Den här artikeln beskriver inte Availability Zones i detalj. Men den innehåller en allmän diskussion om hur du använder Tillgänglighetsuppsättningar jämfört med Tillgänglighetszoner.

Azure-regioner där Availability Zones erbjuds har flera datacenter. Datacenter är oberoende tillhandahållande av strömkälla, kylning och nätverk. Orsak för att erbjuda olika zoner i en enda Azure-region är att distribuera program i två eller tre Tillgänglighetszoner som erbjuds. Distribuera i olika zoner, utfärdar i Power BI och nätverk påverkar bara en Tillgänglighetszon med Azure-infrastruktur, programdistributionen inom en Azure-region är fortfarande fungerar. Vissa minskad kapacitet kan uppstå. Till exempel virtuella datorer i en zon kan gå förlorade, men virtuella datorer i de andra två zonerna fortfarande är igång. 
 
En Azure-Tillgänglighetsuppsättning är en logisk grupperingsfunktion som hjälper till att säkerställa att de VM-resurser som du placerar i Tillgänglighetsuppsättningen är fel isoleras från varandra när de distribueras i ett Azure-datacenter. Azure ser till att de virtuella datorer du placerar i en tillgänglighetsuppsättning körs över flera fysiska servrar, datarack, lagringsenheter och nätverksväxlar. I vissa Azure-dokumentation, den här konfigurationen kallas placeringar i olika [uppdaterings-och feldomäner](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Dessa placeringar är vanligtvis inom en Azure-datacenter. Om vi antar att power och problem påverkar det datacenter som du distribuerar, påverkas alla kapacitet i en Azure-region.

Placeringen av datacenter som representerar Azure Availability Zones är en kompromiss mellan leverera godkända Nätverksfördröjningen mellan tjänster som distribueras i olika zoner och ett avstånd mellan datacenter. Katastrofer är helst skulle påverka power-, network tillgångs- och infrastruktur för samtliga Tillgänglighetszoner i den här regionen. Men eftersom monument katastrofer har visat kan Availability Zones inte alltid ange tillgänglighet som du vill inom en region. Tänk på att orkan Maria som når ön Puerto Rico 20 September 2017. Orkanen orsakade i princip en nästan 100 procent inaktiverad på 90 mil hela ön.

## <a name="single-vm-scenario"></a>Single-VM-scenario

I ett enda VM-scenario skapar du en virtuell Azure-dator för SAP HANA-instans. Du kan använda Azure Premium Storage för att vara värd för operativsystemdisken och alla datadiskar. Azure drifttid serviceavtal på 99,9% och serviceavtal för andra Azure-komponenter är tillräcklig att uppfylla serviceavtal för tillgänglighet för dina kunder. I det här scenariot har du inget behov av att använda en Azure-Tillgänglighetsuppsättning för virtuella datorer som kör DBMS-lagret. I det här scenariot kan du förlita dig på två olika funktioner:

- Azure VM automatisk omstart (kallas även Azure tjänståterställning)
- SAP HANA automatisk omstart

Azure VM automatisk omstart eller tjänståterställning, är en funktion i Azure som fungerar på två nivåer:

- Azure-servervärden kontrollerar hälsotillståndet för en virtuell dator som är värd för server-värd.
- Azure-infrastrukturkontrollanten övervakar hälsa och tillgänglighet för server-värd.

En funktion för kontroll av hälsotillstånd övervakar hälsotillståndet för varje virtuell dator som finns på en Azure server-värd. Om en virtuell dator hamnar i ett icke hälsosamma tillstånd, kan en omstart av den virtuella datorn initieras av Azure-värd-agenten som kontrollerar hälsotillståndet för den virtuella datorn. Infrastrukturkontrollanten kontrollerar hälsotillståndet för värden genom att kontrollera många olika parametrar som kan tyda på problem med värdens maskinvara. Den söker också på tillgängligheten för värden via nätverket. Ett tecken på problem med-värden kan leda till följande händelser:

- Om värden signalerar ett felaktigt hälsotillstånd, utlöses en omstart av värden och en omstart av de virtuella datorerna som körs på värden.
- Om värden inte är i felfritt tillstånd efter omstarten, initieras en Omdistributionen av de virtuella datorer som ursprungligen på noden nu feltillstånd till en felfri värd-server. I det här fallet har den ursprungliga värden markerats som inte felfri. Den används inte för ytterligare distributioner tills den har rensats eller ersatts.
- Om felaktiga värden har problem under omstart, utlöses en omedelbar omstart av de virtuella datorerna på en felfri värd. 

Med värden och VM-övervakning från Azure, Azure virtuella datorer som får problem med värden automatiskt startas om på en felfri Azure-värd. 

>[!IMPORTANT]
>Återställning av Azure-tjänsten startar inte om virtuella Linux-datorer där gäst-OS är i ett panik kernel-läge. Standardinställningarna för de vanligaste Linux-versionerna inte automatiskt startar om virtuella datorer eller servern där i Linux-kerneln är i panik tillstånd. Standard föreskrivs i stället för att hålla Operativsystemet i kernel panik tillstånd för att kunna bifoga en kernel-felsökare för att analysera. Azure respektera som standard genom att starta om en virtuell dator med gästoperativsystemet inte automatiskt i en sådan ett tillstånd. Antas att sådana händelser är mycket ovanligt. Du kan skriva över standardbeteendet för att aktivera en omstart av den virtuella datorn. Om du vill ändra standardvärdet aktivera beteendet parametern kernel.panic om du i /etc/sysctl.conf. Den tidpunkt du anger för den här parametern är i sekunder. Vanliga rekommenderade värden är att vänta på 20 – 30 sekunder innan du aktiverar omstarten via den här parametern. Se även <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Den andra funktionen som du litar på i det här scenariot är det faktum att den HANA-tjänst som körs i en startats om virtuell dator startas automatiskt efter den virtuella datorn startas om. Du kan ställa in [HANA-tjänsten automatisk omstart](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) via watchdog-tjänster på de olika HANA-tjänsterna.

Du kan förbättra det här scenariot för enskild VM genom att lägga till en kall redundansnod till en SAP HANA-konfiguration. I SAP HANA-dokumentation, den här konfigurationen kallas [värdbaserad automatisk redundans](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Den här konfigurationen kan vara klokt i en lokal distribution situation där servermaskinvaran är begränsad, och du anger en enskild server-nod som värd automatisk redundans nod för en uppsättning produktionsvärdar. Men i Azure, där den underliggande infrastrukturen med Azure tillhandahåller en felfri målserver för en lyckad omstart av virtuella datorer, det vara inte klokt att distribuera SAP HANA-värd automatisk redundans. Det finns inga referensarkitektur som förutser en standby nod för HANA värd automatisk redundans på grund av Azure tjänståterställning. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Specialfall av SAP HANA skala ut konfigurationer i Azure
Hög tillgänglighet för SAP HANA skala ut konfigurationer förlitar sig på service återställning av virtuella Azure-datorer och omstart av SAP HANA-instans som den virtuella datorn är igång och köra igen. Arkitektur för hög tillgänglighet baserat på HANA System Replication ska införas vid ett senare tillfälle. 


## <a name="availability-scenarios-for-two-different-vms"></a>Scenarier för tillgänglighet för två olika virtuella datorer

Om du använder två virtuella Azure-datorer i en Azure-Tillgänglighetsuppsättning, kan du öka drifttid mellan dessa två virtuella datorer om de är placerade i en Azure-Tillgänglighetsuppsättning i en Azure-region. Grundläggande konfiguration i Azure ser ut som:

![Diagram över två virtuella datorer med alla lager](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

För att visa olika tillgänglighet scenarier kan utelämnas några lager i diagrammet. Diagrammet visar lager som föreställer virtuella datorer, värdar, Tillgänglighetsuppsättningar och Azure-regioner. Azure Virtual Network-instanser, resursgrupper och prenumerationer ha inte en roll i scenarier som beskrivs i det här avsnittet.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replikera säkerhetskopieringar till en andra virtuell dator

En av de mest rudimentära inställningarna är att använda säkerhetskopior. Du kanske särskilt säkerhetskopieringar av transaktionsloggen levereras från en virtuell dator till en annan virtuell Azure-dator. Du kan välja vilken Azure Storage. I den här konfigurationen kan ansvarar du för skript kopia av schemalagda säkerhetskopieringar som genomförs på den första virtuella datorn till den andra virtuella datorn. Om du vill använda andra VM-instanser måste du återställa den fullständig och inkrementell/differentiella säkerhetskopieringar av transaktionsloggen till den tidpunkt som du behöver. 

Arkitekturen ser ut:

![Diagram över två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Den här konfigurationen är inte lämplig för att uppnå bra mål för återställningspunkt (RPO) och återställning mål för återställningstid (RTO) gånger. RTO gånger särskilt skulle bli sämre på grund av behovet av att fullständigt återställa fullständig databasen med hjälp av de kopierade säkerhetskopiorna. Den här konfigurationen är användbar för att återställa oönskade data raderas på main-instanser. Med den här konfigurationen kan när som helst du återställa till en viss punkt, extrahera data och importera den borttagna data till din huvudsakliga instans. Därför kan vara det bra att använda en säkerhetskopia-metod i kombination med andra funktioner för hög tillgänglighet. 

Medan säkerhetskopiorna kopieras kanske du kan använda en mindre virtuell dator än den största virtuella datorn som SAP HANA-instans som körs på. Tänk på att du kan koppla ett mindre antal virtuella hårddiskar till mindre virtuella datorer. Information om gränserna för enskilda VM-typer finns i [storlekar för Linux-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA-systemreplikering utan automatisk växling vid fel

Scenarier som beskrivs i det här avsnittet använder SAP HANA-systemreplikering. SAP-dokumentation finns i [systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Scenarier utan automatisk redundans är inte vanligt att konfigurationer i en Azure-region. En konfiguration utan automatisk växling vid fel, obligates trots att undvika en Pacemaker installationsprogrammet du övervaka och redundans manuellt. Eftersom det här tar och åtgärder samt lita de flesta kunder på Azure-tjänsten återställning i stället. Det finns vissa gränsfall där den här konfigurationen kan hjälpa när det gäller felscenarier. Eller, i vissa fall kan en kund att upptäcka mer effektiv.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA-systemreplikering utan automatisk redundans och utan data true

I det här scenariot kan använda du SAP HANA-systemreplikering för att flytta data på en synkron sätt att uppnå en RPO på 0. Å andra sidan kan du ha en tillräckligt lång RTO som du inte behöver redundans eller data som förinläsning av i cachen för HANA-instans. I det här fallet är det möjligt att uppnå ekonomin i konfigurationen genom att utföra följande åtgärder:

- Kör en annan SAP HANA-instans i den andra virtuella datorn. SAP HANA-instans i den andra virtuella datorn tar de flesta av minnet för den virtuella datorn. Om en växling till den andra virtuella datorn som du behöver att stänga av den som kör SAP HANA-instans som innehåller de data som är fullständigt inläst i den andra virtuella datorn så att den replikerade datan kan läsas in i cachen för den aktuella HANA-instansen i den andra virtuella datorn.
- Använda en mindre VM-storlek på den andra virtuella datorn. Om det uppstår redundans, har du ytterligare ett steg före manuell redundans. I det här steget ska du ändra storlek på den virtuella datorn till storleken på den Virtuella källdatorn. 
 
Scenariot ser ut som:

![Diagram över två virtuella datorer med storage-replikering](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Även om du inte använder data Förhandsladda i HANA system replication mål, behöver du minst 64 GB minne. Du behöver också tillräckligt med minne förutom 64 GB för att lagra rowstore-data i minnet för målinstansen.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA-systemreplikering utan automatisk redundans och med data som är true

I det här scenariot är data som replikeras till HANA-instans i den andra virtuella datorn i förväg. Detta eliminerar två fördelar med att inte förinläsning av data. I det här fallet kan du köra ett annat SAP HANA-system på den andra virtuella datorn. Du kan också använda en mindre VM-storlek. Därför kan implementera kunder det här scenariot sällan.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA-systemreplikering med automatisk redundans

I standard och de vanligaste konfiguration för tillgänglighet inom en Azure-region, två virtuella Azure-datorer som kör Linux SLES har ett redundanskluster som definierats. SLES Linux-kluster är baserad på den [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) framework, tillsammans med en [STONITH](http://linux-ha.org/wiki/STONITH) enhet. 

Replikeringsläge som används har synkroniserats från en SAP HANA-perspektiv och automatisk redundans har konfigurerats. SAP HANA-instans fungerar som en varm standby nod i den andra virtuella datorn. Noden vänteläge tar emot en synkron dataström med ändringsposter från den primära SAP HANA-instansen. Eftersom transaktioner har utförts av programmet på den primära noden HANA, väntar den primära noden HANA att bekräfta genomförande till programmet förrän den sekundära noden i SAP HANA bekräftar att commit-posten togs emot. SAP HANA erbjuder två lägen för synkron replikering. Mer information och en beskrivning av skillnaderna mellan dessa två lägen för synkron replikering finns i SAP-artikeln [replikeringslägen för SAP HANA-systemreplikering](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

Den övergripande konfigurationen ser ut som:

![Diagram över två virtuella datorer med storage-replikering och redundans](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Du kan välja den här lösningen eftersom den gör det möjligt för dig att uppnå ett Återställningpunktsmål = 0 och ett lågt värde för Återställningstid. Konfigurera anslutningen för SAP HANA-klienter så att SAP HANA-klienter använder den virtuella IP-adressen för att ansluta till HANA system replication konfigurationen. En sådan konfiguration eliminerar behovet av att konfigurera om programmet om det uppstår redundans till den sekundära noden. Azure VM SKU: er för de primära och sekundära virtuella datorerna som i det här scenariot måste vara samma.

## <a name="next-steps"></a>Nästa steg

Stegvisa anvisningar om hur du konfigurerar de här konfigurationerna i Azure finns i:

- [Konfigurera SAP HANA-systemreplikering på virtuella Azure-datorer](sap-hana-high-availability.md)
- [Hög tillgänglighet för SAP HANA med hjälp av systemreplikering](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Mer information om tillgänglighet för SAP HANA i Azure-regioner finns:

- [Tillgänglighet för SAP HANA i Azure-regioner](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

