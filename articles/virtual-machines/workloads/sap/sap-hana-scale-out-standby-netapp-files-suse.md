---
title: SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server | Microsoft Docs
description: Hög tillgänglighets guide för SAP NetWeaver på SUSE Linux Enterprise Server med Azure NetApp Files för SAP-program
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934164"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA skala ut med noden vänte läge på virtuella Azure-datorer med Azure NetApp Files på SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


I den här artikeln beskrivs hur du distribuerar det virtuella HANA-systemet med hög tillgänglighet i skalbar konfiguration med vänte läge på virtuella Azure-datorer med [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) för de delade lagrings volymerna.  
I exemplen konfigurationer, installations kommandon och så vidare är HANA-instansen **03** och Hana-systemets ID är **HN1**. Exemplen baseras på HANA 2,0 SP4 och SUSE Linux Enterprise Server för SAP 12 SP4. 

Läs följande SAP-anteckningar och dokument först:

* [Azure NetApp Files dokumentation][anf-azure-doc] 
* SAP anmärkning [1928533], som har:  
  * Lista över storlekar på virtuella Azure-datorer som stöds för distribution av SAP-program
  * Viktig kapacitets information för Azure VM-storlekar
  * Stöd för SAP-program och operativ system (OS) och databas kombinationer
  * Nödvändig SAP kernel-version för Windows och Linux på Microsoft Azure
* SAP NOTE [2015553] visar krav för SAP-program distributioner som stöds i Azure.
* SAP NOTE [2205917] har rekommenderade OS-inställningar för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [1944799] innehåller SAP-rikt linjer för SUSE Linux Enterprise Server för SAP-program
* SAP NOTE [2178632] innehåller detaljerad information om alla övervaknings mått som rapporter ATS för SAP i Azure.
* SAP NOTE [2191498] har den version av SAP host agent som krävs för Linux i Azure.
* SAP NOTE [2243692] innehåller information om SAP-licensiering på Linux i Azure.
* SAP NOTE [1984787] innehåller allmän information om SUSE Linux Enterprise Server 12.
* SAP anmärkning [1999351] innehåller ytterligare felsöknings information för Azure Enhanced Monitoring-tillägget för SAP.
* SAP anmärkning [1900823] innehåller information om SAP HANA lagrings krav
* [SAP community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) har alla nödvändiga SAP-anteckningar för Linux.
* [Azure Virtual Machines planera och implementera SAP på Linux][planning-guide]
* [Azure Virtual Machines-distribution för SAP på Linux][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP på Linux][dbms-guide]
* [Guider för SUSE SAP-ha bästa praxis][suse-ha-guide] Guiderna innehåller all information som krävs för att konfigurera NetWeaver HA och SAP HANA systemreplikering lokalt. Använd dessa guider som en allmän bas linje. De ger mycket mer detaljerad information.
* [Viktig information om SUSEt hög tillgänglighets tillägg 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp SAP-program på Microsoft Azure med Azure NetApp Files][anf-sap-applications-azure]
* [SAP HANA på NetApp system med NFS](https://www.netapp.com/us/media/tr-4435.pdf). Konfigurations guiden innehåller information om hur du konfigurerar SAP HANA med hjälp av NFS som tillhandahålls av Azure NetApp Files.


## <a name="overview"></a>Översikt

En av metoderna för att uppnå HANA hög tillgänglighet är automatisk växling vid misslyckande. En eller flera virtuella datorer läggs till i HANA-systemet och konfigureras att vara vänte läge för att konfigurera värden automatiskt. När en aktiv nod kraschar tar en nod i vänte läge automatiskt över. I den presenterade konfigurationen med virtuella Azure-datorer, som utförs av [NFS på Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Standby-noden måste ha åtkomst till alla databas volymer. HANA-volymerna måste monteras som NFSv4-volymer. Den förbättrade fil låne metoden för fillån i NFSv4-protokollet används för `I/O` staket. 

> [!IMPORTANT]
> Det är obligatoriskt att distribuera HANA-data och logg volymer som NFSv 4.1-volymer och montera dem med hjälp av NFSv 4.1-protokollet, för att få en konfiguration som stöds. HANA-värden för automatisk redundans med standby-noden stöds inte med NFSv3.

![Översikt över SAP NetWeaver-hög tillgänglighet](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Efter SAP HANA nätverks rekommendationer har tre undernät skapats i ett virtuellt Azure-nätverk: för kommunikation med lagrings systemet för intern HANA kommunikation mellan noder och för klient kommunikation. Azure NetApp-volymerna finns i separata undernät, [delegerade till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

I den här exempel konfigurationen är under näten:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Konfigurera Azure NetApp Files-infrastrukturen 

Innan du fortsätter med installationen av Azure NetApp-infrastrukturen kan du bekanta dig med Azure NetApp Files- [dokumentationen][anf-azure-doc]. Azure NetApp-filer finns i flera [Azure-regioner](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Kontrol lera om den valda Azure-regionen har Azure NetApp Files.  

Följande länk visar tillgängligheten för Azure NetApp Files av Azure-regionen: [Azure NetApp Files tillgänglighet per Azure-region][anf-avail-matrix].  
Innan du distribuerar Azure NetApp Files kan du begära onboarding till Azure NetApp Files enligt [anvisningarna i registrera dig för Azure NetApp-filer][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Distribuera Azure NetApp Files-resurser  

Följande steg förutsätter att du redan har distribuerat [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Azure NetApp Files resurser och de virtuella datorerna där Azure NetApp Files resurserna ska monteras måste distribueras i samma Azure-Virtual Network eller i peer-anslutna virtuella Azure-nätverk.  

1. Om du inte redan har gjort det kan du begära [att registrera Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Skapa NetApp-kontot i den valda Azure-regionen genom [att följa anvisningarna för att skapa NetApp-konto](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  

3. Konfigurera Azure NetApp Files kapacitets pool genom att följa [anvisningarna om hur du konfigurerar Azure NetApp Files kapacitets pool](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Den HANA-arkitektur som presenteras i den här artikeln använder pool för enkel Azure NetApp Files kapacitet, Ultra service-nivå. Vi rekommenderar Azure NetApp Files Ultra eller Premium [service nivå](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) för HANA-arbetsbelastningar på Azure.  

4. Delegera ett undernät till Azure NetApp-filer enligt beskrivningen i [instruktionerna delegera ett undernät till Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Distribuera Azure NetApp Files volymer genom att följa [anvisningarna för att skapa en volym för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Se till att välja **nfsv 4.1** -version när du distribuerar volymerna. För närvarande krävs ytterligare vit listning för åtkomst till NFSv 4.1. Distribuera volymerna i det angivna Azure NetApp Files- [undernätet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Tänk på att Azure NetApp Files-resurser och de virtuella Azure-datorerna måste finnas i samma Azure-Virtual Network eller i peer-datorer med virtuella Azure-nätverk. Till exempel <b>HN1</b>-data-Mnt00001, <b>HN1</b>-log-mnt00001 osv. är volym namnen och NFS://10.23.1.5/<b>HN1</b>-data-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001 osv. är fil Sök vägarna för Azure NetApp Files volymerna.  

   1. volym <b>HN1</b>-data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. volym <b>HN1</b>-data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. volym <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. volym <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. volym <b>HN1</b>– delad (NFS://10.23.1.4/<b>HN1</b>– delad)
   
   I det här exemplet använde vi separat Azure NetApp Files för varje HANA-data och logg volym. För mer kostnads optimerad konfiguration på mindre eller icke-produktiva system är det möjligt att placera alla data monteringar och alla loggar monteras på en enda volym.  

### <a name="important-considerations"></a>Viktiga överväganden

Tänk på följande viktiga överväganden när du överväger Azure NetApp Files för SAP-NetWeaver på SUSE hög tillgänglighets arkitektur:

- Den minsta kapacitets poolen är 4 TiB.  
- Den minsta volym storleken är 100 GiB
- Azure NetApp Files och alla virtuella datorer, där Azure NetApp Files volymer ska monteras, måste finnas i samma Azure-Virtual Network eller i [peer-anslutna virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) i samma region.  
- Det valda virtuella nätverket måste ha ett undernät, delegerat till Azure NetApp Files.
- Data flödet för en Azure NetApp-volym är en funktion av volym kvoten och tjänst nivån, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). När du ändrar storlek på HANA Azure NetApp-volymer ser du till att det resulterande data flödet uppfyller HANA-system kraven.  
- Azure NetApp Files erbjuder [export princip](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): du kan kontrol lera tillåtna klienter, åtkomst typ (Läs & Skriv, skrivskyddad, osv.). 
- Azure NetApp Files funktionen är inte en zon medveten än. För närvarande är Azure NetApp Files funktionen inte distribuerad i alla tillgänglighets zoner i en Azure-region. Var medveten om potentiella fördröjnings konsekvenser i vissa Azure-regioner.  
- Det är viktigt att de virtuella datorerna distribueras i närheten av Azure NetApp-lagringen för låg latens. För SAP HANA arbets belastningar med låg latens är kritisk. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  
- Användar-ID för <b>sid</b>-adm och grupp-id för `sapsys` på de virtuella datorerna måste matcha konfigurationen i Azure NetApp Files. 

> [!IMPORTANT]
> För SAP HANA arbets belastningar med låg latens är kritisk. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet.  

> [!IMPORTANT]
> Om det finns ett matchnings fel mellan användar-ID för <b>sid</b>-adm och grupp-ID: t för `sapsys` mellan den virtuella datorn och Azure NetApp-konfigurationen, visas behörigheterna för filer på Azure NetApp-volymer, monterade på virtuella datorer, som `nobody`. Se till att ange rätt användar-ID för <b>sid</b>-adm och grupp-ID: t för `sapsys` när [ett nytt system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) ska Azure NetApp Files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Storlek för HANA-databas på Azure NetApp Files

Data flödet för en Azure NetApp-volym är en funktion i volym storlek och tjänst nivå, enligt beskrivningen i [service nivå för Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

När du utformar infrastrukturen för SAP i Azure bör du vara medveten om några lägsta lagrings krav för SAP, som översätter minsta data flödes egenskaper:

- Aktivera Läs-och skriv åtgärder på/Hana/log i 250 MB/SEK med 1 MB I/O-storlekar  
- Aktivera Läs aktivitet på minst 400 MB/SEK för/Hana/data för 16 MB och 64 MB I/O-storlekar  
- Aktivera Skriv aktivitet med minst 250 MB/SEK för/Hana/data med 16 MB och 64 MB I/O-storlekar  

[Azure NetApp Files data flödes gränser](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TIB volym kvot:
- Premium Storage nivå – 64 MiB/s  
- Ultra Storage-nivå – 128 MiB/s  

För att uppfylla de lägsta data flödes kraven i SAP för data och logg, och enligt rikt linjerna för `/hana/shared`, skulle de rekommenderade storlekarna se ut så här:

| Volym | Storlek<br /> Premium Storage nivå | Storlek<br /> Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) per 4 arbetsnoder | Max (512 GB, 1xRAM) per 4 arbetsnoder | v3 eller v 4.1 |

Den SAP HANA konfigurationen för den layout som visas i den här artikeln, med Azure NetApp Files Ultra Storage-nivå skulle se ut så här:

| Volym | Storlek<br /> Ultra Storage-nivå | NFS-protokoll som stöds |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 eller v 4.1 |

> [!NOTE]
> De Azure NetApp Files storleks rekommendationer som anges här är riktade till att uppfylla minimi kraven för SAP-Express gentemot sina infrastruktur leverantörer. I verkliga kund distributioner och arbets belastnings scenarier, är det inte tillräckligt. Använd de här rekommendationerna som en start punkt och anpassa baserat på kraven för din särskilda arbets belastning.  

> [!TIP]
> Du kan ändra storlek på Azure NetApp Files volymer dynamiskt, utan att behöva `unmount` volymerna, stoppa de virtuella datorerna eller stoppa SAP HANA. Det gör det möjligt att uppfylla ditt program både förväntat och oförutsedda data flödes krav.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Distribuera virtuella Linux-datorer via Azure Portal

Först måste du skapa Azure NetApp Files volymerna. Skapa [Azures virtuella nätverk under nät](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) i [Azure-Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Distribuera de virtuella datorerna. Skapa de ytterligare nätverks gränssnitten och koppla nätverks gränssnitten till motsvarande virtuella datorer. Varje virtuell dator har tre nätverks gränssnitt som motsvarar de tre virtuella Azure-undernäten (`storage`, `hana` och `client`).  Se [hur du skapar en virtuell Linux-dator i Azure med flera nätverkskort för nätverks gränssnitt](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> För SAP HANA arbets belastningar med låg latens är kritisk. Arbeta med din Microsoft-representant för att säkerställa att de virtuella datorerna och Azure NetApp Files volymerna distribueras i nära närhet för att uppnå låg latens. Skicka nödvändig information när du är på plats med ett [nytt SAP HANA system](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)som använder SAP HANA Azure NetApp Files.  
> 
Nästa steg förutsätter att du redan har skapat resurs gruppen, det virtuella Azure-nätverket och de tre Azure Virtual Network-undernäten: `storage`, `hana` och `client`.  När du distribuerar de virtuella datorerna väljer du lagrings under nätet så att lagrings nätverks gränssnittet är det primära gränssnittet på de virtuella datorerna. Om detta inte är möjligt måste en explicit väg till Azure-NetApp delegerade undernät via gatewayen för lagrings under nätet konfigureras. 

> [!IMPORTANT]
> Kontrol lera att det operativ system du väljer är SAP-certifierat för SAP HANA på de angivna VM-typerna som du använder. Listan med SAP HANA certifierade VM-typer och OS-versioner för dem kan slås upp i [SAP HANA certifierade IaaS-plattformar](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Se till att klicka i informationen om den VM-typ som anges för att få en fullständig lista över SAP HANA operativ system versioner som stöds för den aktuella VM-typen.  

1. Skapa en tillgänglighets uppsättning för SAP HANA. Se till att ange max uppdaterings domän.  

2. Skapa tre Virtual Machines (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Använd en SLES4SAP-avbildning i Azure-galleriet som stöds för SAP HANA. Vi använde SLES4SAP 12 SP4-avbildningen i det här exemplet.  
   - Välj den tillgänglighets uppsättning som skapades tidigare för SAP HANA.  
   - Välj det virtuella nätverks under nätet för Azure Storage. Välj [accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
När du distribuerar de virtuella datorerna genereras nätverks gränssnitts namnet automatiskt. Vi kommer att referera till nätverks gränssnitten som är kopplade till lagrings-Azure-Virtual Network under nätet som **hanadb1-Storage**, **hanadb2-Storage**och **hanadb3-Storage**. 
3. Skapa tre nätverks gränssnitt – ett för varje virtuell dator för det `hana` virtuella nätverkets undernät. I det här exemplet: **hanadb1-Hana**, **hanadb2-Hana**och **hanadb3-Hana**.  
4. Skapa tre nätverks gränssnitt – ett för varje virtuell dator för det virtuella nätverkets undernät i **klienten** . I det här exemplet: **hanadb1-client**, **hanadb2-client**och **hanadb3-client**.  
5. Bifoga de nyligen skapade virtuella nätverks gränssnitten till motsvarande virtuella datorer  

    1. Navigera till den virtuella datorn i [Azure Portal](https://portal.azure.com/#home).  
    2. Välj Virtual Machines i det vänstra navigerings fönstret. Filtrera på namnet på den virtuella datorn, till exempel **hanadb1**. Klicka på den virtuella datorn.  
    3. När du är i Översikt väljer du stoppa för att frigöra den virtuella datorn.  
    4. Välj nätverk, bifoga nätverks gränssnitt. Välj i list rutan under "Anslut nätverks gränssnitt" de redan skapade nätverks gränssnitten för **`hana`** -och **klient** under nät.  Spara. 
    5. Upprepa för de återstående virtuella datorerna. I vårt exempel – **hanadb2** och **hanadb3**.
    6. Lämna kvar de virtuella datorerna i stoppat tillstånd för tillfället. Nu ska vi aktivera [accelererat nätverk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) för alla nyligen anslutna nätverks gränssnitt.  

6. Aktivera accelererat nätverk för de ytterligare nätverks gränssnitten för **`hana`** och **`client`** undernät.  

    1. Öppna [Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i [Azure Portal](https://portal.azure.com/#home)  
    2. Kör följande kommandon för att aktivera accelererat nätverk för ytterligare nätverks gränssnitt, kopplade till **`hana`** och **`client`** undernät.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. Starta de virtuella datorerna  

    1. Välj Virtual Machines i det vänstra navigerings fönstret. Filtrera på namnet på den virtuella datorn, till exempel **hanadb1**. Klicka på den virtuella datorn.  
    2. Välj Start i Översikt.  

## <a name="operating-system-configuration-and-preparation"></a>Konfiguration och förberedelser för operativ system

Följande objekt har prefixet **[A]** -tillämpligt för alla noder, **[1]** , som endast gäller nod 1, **[2]** – endast tillämpligt på nod 2 eller **[3]** – gäller endast nod 3.

1. **[A]** underhåller Hosts-filerna på de virtuella datorerna. Inkludera poster för alla undernät. Följande poster lades till i `/etc/hosts` i det här exemplet.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** ändra inställningar för DHCP-och moln konfiguration för att undvika oönskade värdnamn ändringar.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** Förbered operativ systemet för att köra SAP HANA på NetApp system med NFS enligt beskrivningen i [SAP HANA på NetApp aff system med NFS-konfigurationsguiden](https://www.netapp.com/us/media/tr-4435.pdf). Skapa konfigurations fil för konfigurations inställningarna för NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

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
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** skapa konfigurations fil med Microsoft för Azure-konfigurations inställningar: `/etc/sysctl.d/ms-az.conf`.  

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

4. **[A]** justera sunrpc-inställningarna enligt rekommendationer i [SAP HANA på NetApp aff Systems med NFS-konfigurationsguiden](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Montera Azure NetApp Files volymer

1. **[A]** skapa monterings punkter för Hana-databasens volymer.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** skapa Node-/regionsspecifika kataloger för `/usr/sap` på **HN1**-delad.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** verifiera inställningen för NFS-domänen. Kontrol lera att domänen är konfigurerad som **`localdomain`** och att mappningen är inställd på **ingen**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** inaktivera mappning av NFSv4-ID. Kör monterings kommandot för att skapa katalog strukturen där `nfs4_disable_idmapping` finns.  Du kommer inte att kunna skapa katalogen manuellt under/sys/modules eftersom åtkomst är reserverad för kernel/driv rutiner.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** skapa SAP HANA gruppen och användaren manuellt. ID: na för Group sapsys och User **HN1**ADM måste anges till samma ID: n, under onboarding. Det här exemplet anger att ID: n är inställt på **1001**. Annars går det inte att komma åt volymerna.  ID: na för gruppen sapsys och användar konton **HN1**adm och sapadm måste vara samma på alla virtuella datorer.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** montera de delade Azure NetApp Files volymerna.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** montera de Node-/regionsspecifika volymerna på **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** montera de Node-/regionsspecifika volymerna på **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** montera de Node-/regionsspecifika volymerna på **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** kontrol lera att alla Hana-volymer monteras med NFS- **NFSv4**.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Installation  

I det här exemplet för att distribuera SAP HANA i en skalbar konfiguration med noden vänte läge med Azure använde vi HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Förbered för HANA-installation

1. **[A]** ange rot lösen ordet, innan Hana-installationen (du kan inaktivera rot lösen ordet när installationen har påträffats). Kör som `root` kommando `passwd`.  

2. **[1]** kontrol lera att du kan SSH till **hanadb2** och **hanadb3**, utan att behöva ange lösen ordet.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** installera ytterligare paket som krävs för Hana 2,0 SP4. Mer information finns i SAP anmärkning [2593824](https://launchpad.support.sap.com/#/notes/2593824) . 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Ändra ägarskap för SAP HANA `data` och `log` kataloger till **HN1**adm.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA-installation

1. **[1]** installera SAP HANA enligt instruktionerna i [SAP HANA 2,0 installations-och uppdaterings guide](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). I det här exemplet installerar vi SAP HANA skala ut med Master, One Worker och en nod i vänte läge.  
   Starta **hdblcm** -programmet från installations program katalogen Hana. Använd `internal_network` parameter och skicka adress utrymmet för under nätet som används för den interna HANA-kommunikationen mellan noderna.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Ange följande värden i prompten.

     * Välj en åtgärd: ange **1** (för installation)
     * Välj ytterligare komponenter för installation: ange **2, 3**
     * Ange installations Sök väg: Tryck på RETUR (Standardvärdet är/Hana/Shared)
     * Ange namn på lokal värd: Tryck på RETUR för att acceptera standard
     * Vill du lägga till värdar i systemet? Skriv **y**
     * Ange Coma-separerade värdnamn som ska läggas till: **hanadb2, hanadb3**
     * Ange rot användar namn [root]: Tryck på RETUR för att acceptera standardvärdet
     * Ange lösen ord för rot användare: Ange rot lösen ordet
     * Välj roller för värd hanadb2: ange **1** (för arbetare)
     * Ange värd för redundans grupp för värd hanadb2 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * Ange lagrings partitions nummer för värd hanadb2 [<<assign automatically>>]: Tryck på RETUR för att acceptera standardvärdet
     * Ange arbets grupp för värd hanadb2 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * Välj roller för värd hanadb3: ange **2** (för vänte läge)
     * Ange värd för redundans grupp för värd hanadb3 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * Ange arbets grupp för värd hanadb3 [standard]: Tryck på RETUR för att acceptera standardvärdet
     * Ange SAP HANA system-ID: ange **HN1**
     * Ange instans nummer [00]: ange **03**
     * Ange den lokala värd arbets gruppen [standard]: Tryck på RETUR om du vill acceptera standard
     * Välj system användning/ange index [4]: ange **4** (för anpassad)
     * Ange plats för data volymer [/hana/data/HN1]: Tryck på RETUR för att acceptera standardvärdet
     * Ange plats för logg volymer [/hana/log/HN1]: Tryck på RETUR för att acceptera standard
     * Begränsa maximal minnesallokering? [n]: ange **n**
     * Ange ett certifikat värd namn för värd-hanadb1 [hanadb1]: Tryck på RETUR för att acceptera standardvärdet
     * Ange ett certifikat värd namn för värd-hanadb2 [hanadb2]: Tryck på RETUR för att acceptera standardvärdet
     * Ange ett certifikat värd namn för värd-hanadb3 [hanadb3]: Tryck på RETUR för att acceptera standardvärdet
     * Ange system administratörs lösen ord (hn1adm): Ange lösen ordet
     * Ange lösen ord för system databas användare (SYSTEM): Ange SYSTEMets lösen ord
     * Bekräfta systemets lösen ord (system Database User): Ange SYSTEMets lösen ord
     * Vill du starta om systemet efter omstart av datorn? [n]: ange **n** 
     * Vill du fortsätta (j/n): validera sammanfattningen och om allt ser bra ut skriver du **y**


2. **[1]** kontrol lera global. ini  

   Visa global. ini och se till att konfigurationen för den interna SAP HANA kommunikationen mellan noderna är på plats. Verifiera sektions **kommunikation**. Det bör ha adress utrymmet för **`hana`** undernät och `listeninterface` anges till `.internal`. Verifiera avsnittet **internal_hostname_resolution**. Den bör ha IP-adresserna för de HANA-virtuella datorer som tillhör **`hana`** under nätet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** Lägg till värd mappning för att säkerställa att klientens IP-adresser används för klient kommunikation. Lägg till avsnittet `public_host_resolution` och Lägg till motsvarande IP-adresser från klient under nätet.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** starta om SAP HANA för att aktivera ändringarna.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** kontrol lera att klient gränssnittet kommer att använda IP-adresserna från **klient** under nätet för kommunikation.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Mer information om hur du verifierar konfigurationen finns i SAP NOTE [2183363 – konfiguration av SAP HANA internt nätverk](https://launchpad.support.sap.com/#/notes/2183363) .  

6. För att optimera SAP HANA för det underliggande lagrings utrymmet för Azure NetApp-filer anger du följande SAP HANA parametrar:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **på**
   - `async_write_submit_active` **på**
   - `async_write_submit_blocks` **alla**

   Mer information finns i [SAP HANA på NETAPP aff system with NFS Configuration Guide](https://www.netapp.com/us/media/tr-4435.pdf). 

   Från och med SAP HANA 2,0-system kan du ange parametrarna i `global.ini`. Se SAP NOTE [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   För SAP HANA 1,0-system, versioner upp till SPS12, kan dessa parametrar anges under installationen, enligt beskrivningen i SAP NOTE [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. Lagrings utrymmet som används av Azure NetApp Files har en begränsning för fil storlek eller 16 TB. SAP HANA är inte implicit medveten om lagrings begränsningen och skapar inte automatiskt någon ny datafil när fil storleks gränsen på 16 TB nås. I takt med att SAP HANA försöker växa till filen bortom 16 TB, leder det till fel och index server krasch. 

   > [!IMPORTANT]
   > För att förhindra SAP HANA försöker öka datafiler utöver [16TB-gränsen](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) för lagrings sybsystem anger du följande parametrar i `global.ini`.  
   > -  datavolume_striping = True
   > - datavolume_striping_size_gb = 15000 mer information finns i SAP NOTE [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Tänk på SAP NOTE [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Testa SAP HANA redundans 

1. Simulera en nods krasch på en SAP HANA arbetsnoden  

   Kör följande kommandon som **HN1**ADM för att avbilda miljöns status innan du simulerar noden krasch.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
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

   Kör följande kommando som rot på Worker-noden, som är i det här fallet **hanadb2** för att simulera en nods krasch.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Övervaka systemet för slut för ande av redundansväxling. När redundansväxlingen har slutfört hämtningen av statusen, bör den se ut så här: status visas nedan.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Om du vill undvika fördröjningar med SAP HANA redundans anger du `kernel.panic` till 20 sekunder på **alla** Hana-virtuella datorer när en nod upplever kernel-panik. Konfigurationen görs i `/etc/sysctl`. Starta om de virtuella datorerna för att aktivera ändringen. Redundansväxlingen, när en nod upplever kernel-panik kan ta 10 minuter, om denna ändring inte utförs.  

2. Kill-namnserver  
   Kör följande kommandon som **HN1**ADM för att kontrol lera miljöns status, före testet:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Kör följande kommandon som **HN1**adm på den aktiva huvudnoden, som är i det här fallet **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   **Hanadb3** för vänte läge tar över som huvud nod. Resurs tillstånd efter att redundansväxlingen har slutförts:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
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

   Starta igen HANA-instansen på **hanadb1**, det vill säga på samma virtuella dator, där namnserver stoppades. **Hanadb1** -noden kommer att återansluta till miljön och behåller sin vänte läges roll.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Förvänta följande status när SAP HANA har startat på **hanadb1**:  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Sedan kan du avsluta igen namnserver på den aktuella aktiva huvudnoden, det vill säga på hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1** kommer att återuppta rollen för huvudnoden. Status när redundansväxlingen är slutfört ser ut så här:
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

   Starta SAP HANA på **hanadb3** – den är redo att fungera som en standby-nod.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Status efter att SAP HANA har startats på **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
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

* [Azure Virtual Machines planera och implementera SAP][planning-guide]
* [Azure Virtual Machines distribution för SAP][deployment-guide]
* [Azure Virtual Machines DBMS-distribution för SAP][dbms-guide]
* För att lära dig att upprätta hög tillgänglighet och planera för haveri beredskap för SAP 
* HANA på Azure (stora instanser) finns i [SAP HANA (stora instanser) hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md).
* Information om hur du upprättar hög tillgänglighet och planerar för haveri beredskap för SAP HANA på virtuella Azure-datorer finns i [hög tillgänglighet för SAP HANA på Azure-Virtual Machines (VM)][sap-hana-ha]
