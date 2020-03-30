---
title: Konfigurera Citrix XenDesktop/XenApp-haveriberedskap med Azure Site Recovery
description: I den här artikeln beskrivs hur du konfigurerar haveriberedskap för Citrix XenDesktop- och XenApp-distributioner med Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 29fbe5389da924a2ecc660aa5ce5c4bb0a0902b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084541"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>ställa in haveriberedskap för en distribution av Citrix XenApp och XenDesktop på flera nivåer



Citrix XenDesktop är en virtuell lösning för stationära datorer och program som en ondemand-tjänst till alla användare, var som helst. Med FlexCast-leveransteknik kan XenDesktop snabbt och säkert leverera program och stationära datorer till användarna.
Idag tillhandahåller Citrix XenApp inga funktioner för haveriberedskap.

En bra katastrofåterställning lösning, bör tillåta modellering av återhämtningsplaner runt ovanstående komplexa program arkitekturer och har också möjlighet att lägga till anpassade steg för att hantera programmappningar mellan olika nivåer därmed ger ett enda klick säker skottlösning i händelse av en katastrof som leder till en lägre RTO.

Det här dokumentet innehåller steg-för-steg-vägledning för att skapa en lösning för haveriberedskap för dina lokala Citrix XenApp-distributioner på Hyper-V- och VMware vSphere-plattformar. Det här dokumentet beskriver också hur du utför en test redundans (haveriberedskapsövning) och oplanerad redundans till Azure med hjälp av återställningsplaner, konfigurationer och förutsättningar som stöds.


## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du förstår följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
1. Så här [utformar du ett återställningsnätverk](site-recovery-network-design.md)
1. [Gör en testväxling till Azure](site-recovery-test-failover-to-azure.md)
1. [Gör en redundans till Azure](site-recovery-failover.md)
1. Replikera [en domänkontrollant](site-recovery-active-directory.md)
1. Replikera [SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Distributionsmönster

En Citrix XenApp- och XenDesktop-servergrupp har vanligtvis följande distributionsmönster:

**Mönstret för distribution**

Distribution av Citrix XenApp och XenDesktop med AD DNS-server, SQL-databasserver, Citrix-leveranskontroll, StoreFront-server, XenApp Master (VDA), Citrix XenApp License Server

![Distributionsmönster 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

I den här artikeln användes Citrix-distributioner på virtuella VMware-datorer som hanteras av vSphere 6.0 / System Center VMM 2012 R2 för att installera DR.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Inte i omfattning | Ja
**Vmware** | Inte i omfattning | Ja
**Fysisk server** | Inte i omfattning | Ja

### <a name="versions"></a>Versioner
Kunder kan distribuera XenApp-komponenter som virtuella datorer som körs på Hyper-V eller VMware eller som fysiska servrar. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till Azure.
Eftersom XenApp 7.7 eller senare stöds i Azure kan endast distributioner med dessa versioner skickas över till Azure för haveriberedskap eller migrering.

### <a name="things-to-keep-in-mind"></a>Saker att tänka på

1. Skydd och återställning av lokala distributioner med Server OS-datorer för att leverera XenApp-publicerade appar och XenApp-publicerade skrivbord stöds.

2. Det går inte att skydda och återställa lokala distributioner med stationära OPERATIVSYSTEM-datorer för att leverera stationära VDI för virtuella klientskrivbord, inklusive Windows 10. Detta beror på att Site Recovery inte stöder återställning av datorer med stationära operativsystem.  Också, vissa klient virtuella stationära operativsystem (t.ex. Windows 7) stöds ännu inte för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

3.  Azure Site Recovery kan inte replikera och skydda befintliga lokala MCS- eller PVS-kloner.
Du måste återskapa dessa kloner med Azure RM-etablering från leveransstyrenhet.

4. NetScaler kan inte skyddas med Azure Site Recovery eftersom NetScaler baseras på FreeBSD och Azure Site Recovery stöder inte skydd av FreeBSD OS. Du skulle behöva distribuera och konfigurera en ny NetScaler-installation från Azure Market plats efter redundans till Azure.


## <a name="replicating-virtual-machines"></a>Replikera virtuella datorer

Följande komponenter i Citrix XenApp-distributionen måste skyddas för att möjliggöra replikering och återställning.

* Skydd av AD DNS-server
* Skydd av SQL-databasserver
* Skydd av Citrix Delivery Controller
* Skydd av StoreFront-server.
* Skydd av XenApp Master (VDA)
* Skydd av Citrix XenApp-licensserver


**AD DNS-serverreplikering**

Se [Skydda Active Directory och DNS med Azure Site Recovery](site-recovery-active-directory.md) om vägledning för att replikera och konfigurera en domänkontrollant i Azure.

**SQL-databas Serverreplikering**

Mer information om de rekommenderade alternativen för att skydda SQL [Server med SQL Server-haveriberedskap och Azure Site Recovery finns i Skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery.](site-recovery-sql.md)

Följ [den här vägledningen](site-recovery-vmware-to-azure.md) för att börja replikera de andra virtuella komponentdatorerna till Azure.

![Skydd av XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Beräknings- och nätverksinställningar**

När datorerna har skyddats (statusen visas som "skyddad" under replikerade objekt) måste beräknings- och nätverksinställningarna konfigureras.
I egenskaper för beräkning och nätverk > beräkning kan du ange Azure VM-namn och målstorlek.
Ändra namnet så att det uppfyller kraven för Azure om det behövs. Du kan också visa och lägga till information om målnätverket, undernätet och IP-adressen som ska tilldelas Azure VM.

Observera följande:

* Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig vid redundans fungerar inte redundansen. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.

* För AD/DNS-servern kan du ange samma adress som DNS-servern för Det virtuella Azure-nätverket genom att behålla den lokala adressen.

Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

*   Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
*   Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
* Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.
*   Om den virtuella datorn har flera nätverkskort ansluter de alla till samma nätverk.
*   Om den virtuella datorn har flera nätverkskort blir det första som visas i listan standardnätverkskortet i den virtuella Azure-datorn.


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

När replikering har aktiverats för XenApp-komponenten virtuella datorer, är nästa steg att skapa en återställningsplan.
En återställningsplan grupperar tillsammans virtuella datorer med liknande krav för redundans och återställning.  

**Steg för att skapa en återställningsplan**

1. Lägg till virtuella XenApp-komponentdatorer i återställningsplanen.
2. Klicka på Återställningsplaner -> + återställningsplan. Ange ett intuitivt namn för återställningsplanen.
3. För virtuella VMware-datorer: Välj källa som VMware-processserver, mål som Microsoft Azure och distributionsmodell som Resource Manager och klicka på Välj objekt.
4. För virtuella Hyper-V-datorer: Välj källa som VMM-server, mål som Microsoft Azure och distributionsmodell som Resource Manager och klicka på Välj objekt och välj sedan XenApp-distributions-virtuella datorer.

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i redundansgrupper

Återställningsplaner kan anpassas för att lägga till redundansgrupper för specifika startordningar, skript eller manuella åtgärder. Följande grupper måste läggas till i återställningsplanen.

1. Redundansgrupp1: AD DNS
2. Redundansgrupp2: VIRTUELLA SQL Server-datorer
2. Redundansgrupp3: VIRTUELL VDA-huvudavbildning
3. Redundansgrupp4: Virtuella datorer för leveransstyrenhet och StoreFront-server


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i återställningsplanen

Skript kan köras före eller efter en viss grupp i en återställningsplan. Manuella åtgärder kan också inkluderas och utföras under redundans.

Den anpassade återhämtningsplanen ser ut som nedan:

1. Redundansgrupp1: AD DNS
2. Redundansgrupp2: VIRTUELLA SQL Server-datorer
3. Redundansgrupp3: VIRTUELL VDA-huvudavbildning

   >[!NOTE]     
   >Steg 4, 6 och 7 som innehåller manuella åtgärder eller skriptåtgärder gäller endast för en lokal XenApp >miljö med MCS/PVS-kataloger.

4. Grupp 3 Manuell eller skriptåtgärd: Stäng av huvud-VDA-virtuella datorn.
Huvud-VDA-datorn när det gick över till Azure kommer att vara i kör tillstånd. Om du vill skapa nya MCS-kataloger med Azure-värd måste huvud-VDA-virtuella datorn vara i tillståndet Stoppad (av allokerad). Stäng av den virtuella datorn från Azure-portalen.

5. Redundansgrupp4: Virtuella datorer för leveransstyrenhet och StoreFront-server
6. Grupp3 manual eller skript åtgärd 1:

    ***Lägga till Azure RM-värdanslutning***

    Skapa Azure-värdanslutning i leveransstyrenhetsdatorn för att etablera nya MCS-kataloger i Azure. Följ stegen som förklaras i den här [artikeln](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Grupp3 manual eller skript åtgärd 2:

    ***Återskapa MCS-kataloger i Azure***

    De befintliga MCS- eller PVS-klonerna på den primära platsen replikeras inte till Azure. Du måste återskapa dessa kloner med den replikerade huvud-VDA- och Azure-etableringen från leveransstyrenheten. Följ stegen som förklaras i den här [artikeln](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) för att skapa MCS-kataloger i Azure.

![Återställningsplan för XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Du kan använda skript på [plats](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) för att uppdatera DNS med de nya IP-adresserna för de misslyckade över >virtuella datorer eller för att koppla en belastningsutjämnare på den misslyckade över den virtuella datorn, om det behövs.


## <a name="doing-a-test-failover"></a>Gör en testväxling

Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) för att göra en testväxling.

![Återställningsplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Gör en redundans

Följ [den här vägledningen](site-recovery-failover.md) när du gör en redundansväxling.

## <a name="next-steps"></a>Nästa steg

Du kan [läsa mer](https://aka.ms/citrix-xenapp-xendesktop-with-asr) om att replikera Citrix XenApp- och XenDesktop-distributioner i det här faktabladet. Titta på vägledningen för att [replikera andra program](site-recovery-workload.md) med Site Recovery.
