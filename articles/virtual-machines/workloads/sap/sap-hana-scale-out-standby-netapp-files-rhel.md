---
title: SAP HANA-utskalning med vänteläge med Azure NetApp-filer på RHEL| Microsoft-dokument
description: Guide för hög tillgänglighet för SAP NetWeaver på Red Hat Enterprise Linux med Azure NetApp-filer för SAP-program
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: 78f60cbd20e7801f4af26372884275d7d6782777
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754146"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Distribuera ett SAP HANA-skalningssystem med standby-nod på Virtuella Azure-datorer med hjälp av Azure NetApp-filer på Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2455582
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


I den här artikeln beskrivs hur du distribuerar ett SAP HANA-system med hög tillgänglighet i en skalningskonfiguration med vänteläge på virtuella Azure Red Hat Enterprise Linux-datorer (VMs), med hjälp av [Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) för delade lagringsvolymer.  

I exempelkonfigurationerna, installationskommandona och så vidare är HANA-instansen **03** och HANA-system-ID:t är **HN1**. Exemplen är baserade på HANA 2.0 SP4 och Red Hat Enterprise Linux för SAP 7.6. 

Innan du börjar läser du följande SAP-anteckningar och -dokument:

* [Dokumentation om Azure NetApp Files][anf-azure-doc] 
* SAP Note [1928533] innehåller:  
  * En lista över Azure VM-storlekar som stöds för distribution av SAP-programvara
  * Viktig kapacitetsinformation för Azure VM-storlekar
  * Sap-programvara och kombinationer av operativsystem (operativsystem) och databas
  * Den nödvändiga SAP-kärnversionen för Windows och Linux på Microsoft Azure
* SAP Note [2015553]: Visar förutsättningar för SAP-programdistributioner som stöds av SAP i Azure
* SAP Note [2002167] har rekommenderat OS-inställningar för Red Hat Enterprise Linux
* SAP Note [2009879] har SAP HANA Riktlinjer för Red Hat Enterprise Linux
* SAP Note [2178632]: Innehåller detaljerad information om alla övervakningsmått som rapporterats för SAP i Azure
* SAP Note [2191498]: Innehåller den nödvändiga SAP-värdagentversionen för Linux i Azure
* SAP Note [2243692]: Innehåller information om SAP-licensiering på Linux i Azure
* SAP Note [1999351]: Innehåller ytterligare felsökningsinformation för Azure Enhanced Monitoring Extension för SAP
* SAP Note [1900823]: Innehåller information om SAP HANA-lagringskrav
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Innehåller alla nödvändiga SAP-anteckningar för Linux
* [Planering och implementering av Virtuella Azure-datorer för SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* Allmän RHEL-dokumentation
  * [Tilläggsöversikt för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administration av tillägg med hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Tilläggsreferens för hög tillgänglighet](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux-nätverksguide](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Azure-specifik RHEL-dokumentation:
  * [Installera SAP HANA på Red Hat Enterprise Linux för användning i Microsoft Azure](https://access.redhat.com/solutions/3193782)
* [NetApp SAP-program på Microsoft Azure med Azure NetApp-filer][anf-sap-applications-azure]


## <a name="overview"></a>Översikt

En metod för att uppnå HANA hög tillgänglighet är genom att konfigurera värd automatisk redundans. Om du vill konfigurera automatisk redundans för värden lägger du till en eller flera virtuella datorer i HANA-systemet och konfigurerar dem som standby-noder. När aktiv nod misslyckas tar en standby-nod automatiskt över. I den presenterade konfigurationen med virtuella Azure-datorer uppnår du automatisk redundans med hjälp av [NFS på Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

> [!NOTE]
> Standby-noden behöver åtkomst till alla databasvolymer. HANA-volymerna måste monteras som NFSv4-volymer. Den förbättrade filleasing-baserade låsmekanismen i NFSv4-protokollet används för `I/O` stängsel. 

> [!IMPORTANT]
> Om du vill skapa konfigurationen som stöds måste du distribuera HANA-data- och loggvolymerna som NFSv4.1-volymer och montera dem med hjälp av NFSv4.1-protokollet. Hana-värdens automatisk redundanskonfiguration med standby-nod stöds inte med NFSv3.

![ÖVERSIKT ÖVER HÖG TILLGÄNGLIGHET I SAP NetWeaver](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

I föregående diagram, som följer SAP HANA-nätverksrekommendationer, representeras tre undernät i ett virtuellt Azure-nätverk: 
* För kundkommunikation
* För kommunikation med lagringssystemet
* För intern HANA-internodkommunikation

Azure NetApp-volymerna finns i separat undernät, [delegerade till Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

I det här exemplet är undernäten:  

  - `client`10.9.1.0/26  
  - `storage`10.9.3.0/26  
  - `hana`10.9.2.0/26  
  - `anf`10.9.0.0/26 (delegerat undernät till Azure NetApp-filer)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Konfigurera Azure NetApp Files-infrastrukturen 

Innan du fortsätter med installationen för Azure NetApp Files-infrastrukturen kan du bekanta dig med [Azure NetApp Files-dokumentationen][anf-azure-doc]. 

Azure NetApp-filer är tillgängliga i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Kontrollera om din valda Azure-region erbjuder Azure NetApp-filer.  

Information om tillgängligheten för Azure NetApp-filer per Azure-region finns i [Azure NetApp-filers tillgänglighet per Azure-region][anf-avail-matrix].  

Innan du distribuerar Azure NetApp-filer, begär introduktion till Azure NetApp-filer genom att gå till [Registrera dig för Azure NetApp-filer instruktioner][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp-filresurser  

Följande instruktioner förutsätter att du redan har distribuerat ditt [virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Azure NetApp-filresurserna och virtuella datorer, där Azure NetApp Files-resurserna ska monteras, måste distribueras i samma virtuella Azure-nätverk eller i peer-azure virtuella nätverk.  

1. Om du inte redan har distribuerat resurserna begär du [introduktion till Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Skapa ett NetApp-konto i din valda Azure-region genom att följa instruktionerna i [Skapa ett NetApp-konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Konfigurera en kapacitetspool för Azure NetApp-filer genom att följa instruktionerna i [Konfigurera en Azure NetApp-kapacitetspool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  

   HANA-arkitekturen som presenteras i den här artikeln använder en enda Azure NetApp-filkapacitetspool på *Ultra-tjänstnivå.* För HANA-arbetsbelastningar på Azure rekommenderar vi att du använder en Azure NetApp Files *Ultra* eller [Premium-tjänstnivå](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). *Premium*  

4. Delegera ett undernät till Azure NetApp-filer, enligt beskrivningen i instruktionerna i [Delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuera Azure NetApp-filvolymer genom att följa instruktionerna i [Skapa en NFS-volym för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   När du distribuerar volymerna måste du välja **NFSv4.1-versionen.** Distribuera volymerna i det angivna [Undernätet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)För Azure NetApp-filer . 
   
   Tänk på att Azure NetApp Files-resurserna och virtuella Azure-datorer måste finnas i samma virtuella Azure-nätverk eller i peer-azure virtuella nätverk. Till exempel **är HN1**-data-mnt00001, **HN1**-log-mnt00001 och så vidare, volymnamn och nfs://10.9.0.4/**HN1**-data-mnt00001, nfs://10.9.0.4/**HN1**-log-mnt00001 och så vidare, är filsökvägarna för Azure NetApp Files-volymerna.  

   * volym **HN1**-data-mnt00001 (nfs://10.9.0.4/**HN1**-data-mnt00001)
   * volym **HN1**-data-mnt00002 (nfs://10.9.0.4/**HN1**-data-mnt00002)
   * volym **HN1**-log-mnt00001 (nfs://10.9.0.4/**HN1**-log-mnt00001)
   * volym **HN1**-log-mnt00002 (nfs://10.9.0.4/**HN1**-log-mnt00002)
   * volym **HN1**-delad (nfs://10.9.0.4/**HN1**-delad)
   
   I det här exemplet använde vi en separat Azure NetApp-filvolym för varje HANA-data och loggvolym. För en mer kostnadsoptimerad konfiguration på mindre eller icke-produktiva system är det möjligt att placera alla datafästen på en enda volym och alla loggar fästen på en annan enskild volym.  

### <a name="important-considerations"></a>Att tänka på

När du skapar dina Azure NetApp-filer för SAP HANA-utskalning med scenariot stand by noder bör du vara medveten om följande viktiga överväganden:

- Den minsta kapaciteten poolen är 4 tebibytes (TiB).  
- Den minsta volymstorleken är 100 gibibyte (GiB).
- Azure NetApp-filer och alla virtuella datorer där Azure NetApp-filvolymerna ska monteras måste finnas i samma virtuella Azure-nätverk eller i [peer-virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät som delegeras till Azure NetApp-filer.
- Dataflödet för en Azure NetApp-filvolym är en funktion av volymkvoten och tjänstnivån, som dokumenteras på [tjänstnivå för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). När du dimensionerar HANA Azure NetApp-volymerna kontrollerar du att det resulterande dataflödet uppfyller HANA-systemkraven.  
- Med [exportprincipen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)för Azure NetApp-filer kan du styra de tillåtna klienterna, åtkomsttypen (skrivskyddad, skrivskyddad och så vidare). 
- Azure NetApp Files-funktionen är inte zonmedveten ännu. För närvarande distribueras inte funktionen i alla tillgänglighetszoner i en Azure-region. Var medveten om de potentiella latenskonsekvenserna i vissa Azure-regioner.  

> [!IMPORTANT]
> För SAP HANA-arbetsbelastningar är låg latens kritisk. Arbeta med din Microsoft-representant för att se till att de virtuella datorerna och Azure NetApp Files-volymerna distribueras i närheten.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Dimensionering för HANA-databas på Azure NetApp-filer

Dataflödet för en Azure NetApp-filvolym är en funktion av volymstorlek och tjänstnivå, som dokumenteras på [tjänstnivå för Azure NetApp-filer](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om vissa minimikrav på lagring enligt SAP, vilket leder till minsta dataflödesegenskaper:

- Läs-skriv på /hana/log på 250 megabyte per sekund (MB/s) med 1 MB I/O-storlekar.  
- Läs aktivitet på minst 400 MB/s för /hana/data för 16 MB- och 64 MB I/O-storlekar.  
- Skriv aktivitet på minst 250 MB/s för /hana/data med 16 MB- och 64 MB I/O-storlekar. 

[Azure NetApp-dataflödesgränserna](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TiB volymkvot är:
- Premium Lagringsnivå - 64 MiB/s  
- Ultra Lagringsnivå - 128 MiB/s  

För att uppfylla sap minimikrav för dataflöde för data och logg, och riktlinjerna för /hana /shared, skulle de rekommenderade storlekarna vara:

| Volym | Storlek på<br>Premium lagringsnivå | Storlek på<br>Ultra lagringsnivå | NFS-protokoll som stöds |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/delad | 1xRAM per 4 arbetsnoder | 1xRAM per 4 arbetsnoder | v3 eller v4.1 |

SAP HANA-konfigurationen för layouten som presenteras i den här artikeln, med Azure NetApp Files Ultra Storage-nivå, skulle vara:

| Volym | Storlek på<br>Ultra lagringsnivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/delad | 2 TiB | v3 eller v4.1 |

> [!NOTE]
> Azure NetApp-filstorleksrekommendationerna som anges här är inriktade på att uppfylla de minimikrav som SAP rekommenderar för sina infrastrukturleverantörer. I verkliga kunddistributioner och arbetsbelastningsscenarier kanske dessa storlekar inte är tillräckliga. Använd dessa rekommendationer som utgångspunkt och anpassa, baserat på kraven för din specifika arbetsbelastning.  

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files-volymer dynamiskt, utan att behöva *avmontera* volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Den här metoden ger flexibilitet för att uppfylla både de förväntade och oförutsedda kraven på dataflöde i ditt program.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Distribuera virtuella Linux-datorer via Azure-portalen

Först måste du skapa Azure NetApp Files volymer. Gör sedan följande steg:
1. Skapa [Azure-undernäten för virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) i ditt [virtuella Azure-nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). 
1. Distribuera de virtuella datorerna. 
1. Skapa ytterligare nätverksgränssnitt och koppla nätverksgränssnitten till motsvarande virtuella datorer.  

   Varje virtuell dator har tre nätverksgränssnitt, vilket motsvarar de tre`client` `storage` virtuella `hana`Azure-nätverksundernäten ( och ). 

   Mer information finns [i Skapa en virtuell Linux-dator i Azure med flera nätverkskort](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> För SAP HANA-arbetsbelastningar är låg latens kritisk. För att uppnå låg latens, arbeta med din Microsoft-representant för att se till att de virtuella datorerna och Azure NetApp Files-volymerna distribueras i närheten. När du [lägger till nya SAP HANA-system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) som använder SAP HANA Azure NetApp-filer skickar du nödvändig information. 
 
Nästa instruktioner förutsätter att du redan har skapat resursgruppen, det virtuella Azure-nätverket och `client` `storage` de `hana`tre virtuella Azure-nätverksundernäten: och . När du distribuerar de virtuella datorerna väljer du klientundernätet så att klientnätverksgränssnittet är det primära gränssnittet på de virtuella datorerna. Du måste också konfigurera en explicit väg till Azure NetApp Files delegerade undernät via lagringsundernätsgatewayen. 

> [!IMPORTANT]
> Kontrollera att operativsystemet du väljer är SAP-certifierat för SAP HANA på de specifika VM-typerna du använder. En lista över SAP HANA-certifierade VM-typer och OS-versioner för dessa typer finns på sap [hana-certifierade IaaS-plattformar.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Klicka på information om den listade VM-typen för att få en fullständig lista över OS-versioner som stöds av SAP HANA för den typen.  

1. Skapa en tillgänglighetsuppsättning för SAP HANA. Se till att ställa in den maximala uppdateringsdomänen.  

2. Skapa tre virtuella datorer (**hanadb1**, **hanadb2**, **hanadb3**) genom att göra följande steg:  

   a. Använd en Red Hat Enterprise Linux-avbildning i Azure-galleriet som stöds för SAP HANA. Vi använde en RHEL-SAP-HA 7.6-avbildning i det här exemplet.  

   b. Välj den tillgänglighetsuppsättning som du skapade tidigare för SAP HANA.  

   c. Välj klientens virtuella azure-nätverksundernät. Välj [Accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   När du distribuerar de virtuella datorerna genereras nätverksgränssnittsnamnet automatiskt. I de här instruktionerna för enkelhetens skull hänvisar vi till de automatiskt genererade nätverksgränssnitten, som är kopplade till klientens virtuella azure-nätverksundernät, som **hanadb1-klient**, **hanadb2-klient**och **hanadb3-klient**. 

3. Skapa tre nätverksgränssnitt, ett för varje `storage` virtuell dator, för det virtuella nätverksundernätet (i det här exemplet **hanadb1-storage,** **hanadb2-storage**och **hanadb3-storage**).  

4. Skapa tre nätverksgränssnitt, ett för varje `hana` virtuell dator, för det virtuella nätverksundernätet (i det här exemplet **hanadb1-hana,** **hanadb2-hana**och **hanadb3-hana**).  

5. Koppla de nyskapade virtuella nätverksgränssnitten till motsvarande virtuella datorer genom att göra följande steg:  

    a. Gå till den virtuella datorn i [Azure-portalen](https://portal.azure.com/#home).  

    b. Välj Virtuella datorer **i**den vänstra rutan . Filtrera på namnet på den virtuella datorn (till exempel **hanadb1**) och välj sedan den virtuella datorn.  

    c. Välj **Stoppa** för att frigöra den virtuella datorn i fönstret **Översikt.**  

    d. Välj **Nätverk**och anslut sedan nätverksgränssnittet. I listrutan **Bifoga nätverksgränssnitt** väljer du de redan skapade `storage` `hana` nätverksgränssnitten för undernäten och undernäten.  
    
    e. Välj **Spara**. 
 
    f. Upprepa steg b genom e för de återstående virtuella datorerna (i vårt exempel **hanadb2** och **hanadb3**).
 
    g. Lämna de virtuella datorerna i stoppat tillstånd för tillfället. Därefter aktiverar vi [accelererade nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) för alla nyligen anslutna nätverksgränssnitt.  

6. Aktivera accelererade nätverk för ytterligare nätverksgränssnitt `storage` `hana` för och undernät genom att göra följande steg:  

    a. Öppna [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i [Azure-portalen](https://portal.azure.com/#home).  

    b. Kör följande kommandon för att aktivera snabbare nätverk för ytterligare nätverksgränssnitt, `storage` som `hana` är kopplade till och undernäten.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Starta de virtuella datorerna genom att göra följande:  

    a. Välj Virtuella datorer **i**den vänstra rutan . Filtrera på namnet på den virtuella datorn (till exempel **hanadb1**) och markera det sedan.  

    b. Välj **Start**i **fönstret Översikt** .  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguration och förberedelse av operativsystemet

Instruktionerna i nästa avsnitt föregås av något av följande:
* **[A]**: Gäller för alla noder
* **[1]**: Gäller endast för nod 1
* **[2]**: Gäller endast för nod 2
* **[3]**: Gäller endast för nod 3

Konfigurera och förbereda operativsystemet genom att göra följande:

1. **[A]** Underhåll värdfilerna på de virtuella datorerna. Inkludera poster för alla undernät. Följande poster har lagts `/etc/hosts` till i det här exemplet.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Lägg till en nätverksväg så att kommunikationen till Azure NetApp-filerna går via lagringsnätverksgränssnittet.  

   I det här `Networkmanager` exemplet används för att konfigurera ytterligare nätverksväg. Följande instruktioner förutsätter att gränssnittet `eth1`för lagringsnätverk är .  
   Bestäm först anslutningsnamnet `eth1`för enheten . I det här exemplet `eth1` är `Wired connection 1`anslutningsnamnet för enheten .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Konfigurera sedan ytterligare väg till Azure NetApp-filer delegerade nätverket via `eth1`.  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Starta om den virtuella datorn för att aktivera ändringarna.  

3. **[A]** Installera NFS-klientpaketet.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** Förbered operativsystemet för att köra SAP HANA på Azure NetApp med NFS, enligt beskrivningen i [NetApp SAP Applications på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]. Skapa konfigurationsfilen */etc/sysctl.d/netapp-hana.conf* för Konfigurationsinställningarna för NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 net.ipv4.tcp_slow_start_after_idle=0 net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Skapa konfigurationsfil */etc/sysctl.d/ms-az.conf* med ytterligare optimeringsinställningar.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** Justera sunrpc-inställningarna, enligt rekommendationerna i [NetApp SAP-programmen på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Röd hatt för HANA-konfiguration.

    Konfigurera RHEL enligt beskrivningen i SAP Note [2292690], [2455582,] [2593824] och <https://access.redhat.com/solutions/2447641>.

    > [!NOTE]
    > Om du installerar HANA 2.0 SP04 `compat-sap-c++-7` måste du installera paketet enligt beskrivningen i SAP note [2593824]innan du kan installera SAP HANA. 

## <a name="mount-the-azure-netapp-files-volumes"></a>Montera Azure NetApp-filvolymerna

1. **[A]** Skapa monteringspunkter för HANA-databasvolymerna.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Skapa nodspecifika kataloger för /usr/sap på **HN1**-delad.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Verifiera NFS-domäninställningen. Kontrollera att domänen är konfigurerad som standarddomän för Azure **`defaultv4iddomain.com`** NetApp Files, dvs och mappningen är inställd på **ingen**.  

    > [!IMPORTANT]
    > Se till att ange NFS-domänen `/etc/idmapd.conf` på den virtuella datorn så **`defaultv4iddomain.com`** att den matchar standarddomänkonfigurationen på Azure NetApp-filer: . Om det finns en obalans mellan domänkonfigurationen på NFS-klienten (dvs. den virtuella datorn) och NFS-servern, d.v.s. Azure NetApp-konfigurationen, visas behörigheterna `nobody`för filer på Azure NetApp-volymer som är monterade på de virtuella datorerna som .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verifiera . Det bör ställas in på **Y**. Om du vill `nfs4_disable_idmapping` skapa katalogstrukturen där den finns kör du kommandot montera. Du kommer inte att kunna skapa katalogen manuellt under /sys/modules, eftersom åtkomst är reserverad för kärnan/drivrutinerna.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Mer information om hur `nfs4_disable_idmapping` du https://access.redhat.com/solutions/1749883ändrar parameter finns i .

6. **[A]** Montera de delade Azure NetApp-filernas volymer.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** Montera nodspecifika volymer på **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Montera nodspecifika volymer på **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Montera nodspecifika volymer på **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Kontrollera att alla HANA-volymer är monterade med NFS-protokollversion **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Installation  

I det här exemplet för distribution av SAP HANA i skalningskonfiguration med standby-nod med Azure har vi använt HANA 2.0 SP4.  

### <a name="prepare-for-hana-installation"></a>Förbered för HANA-installation

1. **[A]** Före HANA-installationen anger du rotlösenordet. Du kan inaktivera rotlösenordet när installationen har slutförts. Kör `root` som `passwd`kommando .  

2. **[1]** Kontrollera att du kan logga in via SSH till **hanadb2** och **hanadb3**, utan att bli tillfrågad om ett lösenord.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Installera ytterligare paket, som krävs för HANA 2.0 SP4. Mer information finns i SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824). 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Ändra ägarskapet `data` för `log` SAP HANA och kataloger till **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** Inaktivera brandväggen tillfälligt, så att den inte stör HANA-installationen. Du kan återaktivera den när HANA-installationen är klar. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA-installation

1. **[1]** Installera SAP HANA genom att följa instruktionerna i [installations- och uppdateringsguiden för SAP HANA 2.0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). I det här exemplet installerar vi SAP HANA-utskalning med huvud,en arbetare och en standby-nod.  

   a. Starta **hdblcm-programmet** från HANA-installationsprogramkatalogen. Använd `internal_network` parametern och skicka adressutrymmet för undernätet, som används för den interna HANA-internodkommunikationen.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Ange följande värden vid prompten:

     * För **Välj en åtgärd:** ange **1** (för installation)
     * För **ytterligare komponenter för installation:** ange **2, 3**
     * För installationssökväg: tryck på Retur (standard till /hana/shared)
     * För **lokalt värdnamn:** tryck på Retur för att acceptera standardvärdet
     * Under **Vill du lägga till värdar i systemet?**: enter **y**
     * För **kommaavgränsade värdnamn att lägga till:** ange **hanadb2, hanadb3**
     * För **Root User Name** [root]: tryck på Retur för att acceptera standardvärdet
     * För roller för värdhanadb2: ange **1** (för arbetare)
     * För **värdväxlingsgrupp** för värdhanadb2 [standard]: tryck på Retur för att acceptera standardvärdet
     * För **lagringspartitionsnummer** för värdhanadb2 [<<assign automatically>>]: tryck på Retur för att acceptera standardvärdet
     * För **arbetargrupp** för värdhanadb2 [standard]: tryck på Retur för att acceptera standardvärdet
     * För **Välj roller** för värdhanadb3: ange **2** (för vänteläge)
     * För **värdväxlingsgrupp** för värdhanadb3 [standard]: tryck på Retur för att acceptera standardvärdet
     * För **arbetargrupp** för värdhanadb3 [standard]: tryck på Retur för att acceptera standardvärdet
     * För **SAP HANA System-ID:** ange **HN1**
     * Till **exempelnummer** [00]: ange **03**
     * För **lokal värdarbetaregrupp** [standard]: tryck på Retur för att acceptera standardvärdet
     * För **Välj systemanvändning / Enter index [4]**: ange **4** (för anpassad)
     * För **plats för datavolymer** [/hana/data/HN1]: tryck på Retur för att acceptera standardvärdet
     * För plats för **loggvolymer** [/hana/log/HN1]: tryck på Retur för att acceptera standardvärdet
     * För **Begränsa maximal minnesallokering?** [n]: ange **n**
     * För **certifikatvärdnamn för värdhanadb1** [hanadb1]: tryck på Retur för att acceptera standardvärdet
     * För **certifikatvärdnamn för värdhanadb2** [hanadb2]: tryck på Retur för att acceptera standardvärdet
     * För **certifikatvärdnamn för värdhanadb3** [hanadb3]: tryck på Retur för att acceptera standardvärdet
     * För **systemadministratör (hn1adm) Lösenord:** ange lösenordet
     * För **systemdatabasanvändare (system) Lösenord:** ange systemets lösenord
     * För **Bekräfta System Database User (system) Lösenord:** ange systemets lösenord
     * För **Omstart system efter omstart av maskinen?** [n]: ange **n** 
     * För **Vill du fortsätta (y / n)**: validera sammanfattningen och om allt ser bra ut, ange **y**


2. **[1]** Verifiera global.ini  

   Visa global.ini och se till att konfigurationen för den interna SAP HANA-internodkommunikationen är på plats. Kontrollera **kommunikationsavsnittet.** Den ska ha adressutrymmet för `hana` `listeninterface` undernätet och `.internal`ställas in på . Kontrollera **internal_hostname_resolution** avsnittet. Den bör ha IP-adresserna för de virtuella `hana` HANA-datorer som tillhör undernätet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Lägg till värdmappning för att säkerställa att klient-IP-adresserna används för klientkommunikation. Lägg `public_host_resolution`till avsnitt och lägg till motsvarande IP-adresser från klientundernätet.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Starta om SAP HANA för att aktivera ändringarna.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Kontrollera att klientgränssnittet kommer att `client` använda IP-adresserna från undernätet för kommunikation.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Information om hur du verifierar konfigurationen finns i SAP Note [2183363 - Konfiguration av SAP HANA:s interna nätverk](https://launchpad.support.sap.com/#/notes/2183363).  

5. **[A]** Återaktivera brandväggen.  
   - Stoppa HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - Återaktivera brandväggen
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Öppna nödvändiga brandväggsportar

       > [!IMPORTANT]
       > Skapa brandväggsregler för att tillåta HANA inter nodkommunikation och klienttrafik. De portar som krävs visas på [TCP/IP-portar för alla SAP-produkter](https://help.sap.com/viewer/ports). Följande kommandon är bara ett exempel. I det här fallet med använt systemnummer 03.

       <pre><code>
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       </code></pre>

   - Starta HANA
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Om du vill optimera SAP HANA för den underliggande Azure NetApp Files-lagringen anger du följande SAP HANA-parametrar:

   - `max_parallel_io_requests`**128 år**
   - `async_read_submit`**på den**
   - `async_write_submit_active`**på den**
   - `async_write_submit_blocks`**alla**

   Mer information finns i [NetApp SAP Applications på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]. 

   Från och med SAP HANA 2.0-system `global.ini`kan du ställa in parametrarna i . Mer information finns i SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   
   För SAP HANA 1.0-systemversioner SPS12 och tidigare kan dessa parametrar ställas in under installationen, enligt beskrivningen i SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lagringen som används av Azure NetApp Files har en filstorleksbegränsning på 16 terabyte (TB). SAP HANA är inte implicit medveten om lagringsbegränsningen och skapar inte automatiskt en ny datafil när filstorleksgränsen på 16 TB har uppnåtts. När SAP HANA försöker utöka filen efter 16 TB, kommer det försöket att resultera i fel och, så småningom, i en indexserverkrasch. 

   > [!IMPORTANT]
   > Om du vill förhindra att SAP HANA försöker utöka datafiler utöver [16 TB-gränsen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) för lagringsundersystemet ställer du in följande parametrar i `global.ini`.  
   > - datavolume_striping = sant
   > - datavolume_striping_size_gb = 15000 Mer information finns i SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Var medveten om SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testa SAP HANA-redundans 

1. Simulera en nodkrasch på en SAP HANA-arbetsnod. Gör följande: 

   a. Innan du simulerar nodkraschen kör du följande kommandon som **hn1**adm för att fånga miljöns status:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Om du vill simulera en nodkrasch kör du följande kommando som rot på arbetsnoden, som är **hanadb2** i det här fallet:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Övervaka systemet för återställning. När redundansen har slutförts samlar du in statusen, som ska se ut så här:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > När en nod får kernel-panik undviker du `kernel.panic` fördröjningar med SAP HANA-redundans genom att ställa in 20 sekunder på *alla* VIRTUELLA HANA-datorer. Konfigurationen görs `/etc/sysctl`i . Starta om de virtuella datorerna för att aktivera ändringen. Om den här ändringen inte utförs kan redundansen ta 10 minuter eller mer när en nod får kernel-panik.  

2. Döda namnservern genom att göra följande:

   a. Före testet kontrollerar du miljöns status genom att köra följande kommandon som **hn1**adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Kör följande kommandon som **hn1**adm på den aktiva huvudnoden, som är **hanadb1** i det här fallet:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Standby-noden **hanadb3** tar över som huvudnod. Här är resurstillståndet när redundanstestet har slutförts:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Starta om HANA-instansen på **hanadb1** (det vill på samma virtuella dator, där namnservern dödades). **Hanadb1-noden** kommer att ansluta till miljön igen och behåller sin standby-roll.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   När SAP HANA har startat på **hanadb1**förväntar du dig följande status:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Återigen, döda namnservern på den aktiva huvudnoden (det vill vara på nod **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Nod **hanadb1** återupptar rollen som huvudnod. När redundanstestet har slutförts kommer statusen att se ut så här:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Starta SAP HANA på **hanadb3**, som kommer att vara redo att fungera som en standby-nod.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   När SAP HANA har startat på **hanadb3**ser statusen ut så här:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Nästa steg

* [Planering och implementering av virtuella Azure-datorer för SAP][planning-guide]
* [Azure Virtual Machines-distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* Mer information om hur du upprättar hög tillgänglighet och planerar för haveriberedskap av SAP HANA på virtuella Azure-datorer finns i [Hög tillgänglighet för SAP HANA på virtuella Azure-datorer (VIRTUELLA datorer).][sap-hana-ha]
