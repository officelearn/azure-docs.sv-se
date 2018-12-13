---
title: Konfigurera haveriberedskap för en flerskiktade-Citrix XenDesktop och XenApp-distribution med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar haveriberedskap för Citrix XenDesktop och XenApp-distributioner med Azure Site Recovery.
author: ponatara
manager: abhemraj
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ponatara
ms.openlocfilehash: 68f12bb7335da0a996aeadd752f59db0aa360a8e
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310529"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-citrix-xenapp-and-xendesktop-deployment"></a>Konfigurera haveriberedskap för en distribution av flera nivåer Citrix XenApp och XenDesktop



Citrix XenDesktop är en lösning för fjärrskrivbord som levererar skrivbord och program som en ondemand-tjänst till alla användare, var som helst. Med teknik för leverans av FlexCast kan XenDesktop snabbt och säkert leverera program och skrivbord till användare.
Idag, ger Citrix XenApp inte eventuella katastrofer återställningsfunktioner.

En bra katastrofåterställningslösning ska tillåta modellering av återställningsplaner runt ovanstående komplexa arkitekturer och har också möjlighet att lägga till anpassade steg för att hantera mappning mellan olika nivåer därför att tillhandahålla en enkel klickning Kontrollera som lösning i händelse av en katastrof som leder till en lägre RTO.

Det här dokumentet innehåller stegvisa anvisningar för att skapa en lösning för haveriberedskap för din lokala Citrix XenApp-distributioner på Hyper-V och VMware vSphere-plattformar. Det här dokumentet beskriver också hur du utför ett redundanstest (programåterställningstest) och en oplanerad redundansväxling till Azure med hjälp av återställningsplaner, konfigurationer som stöds och nödvändiga komponenter.


## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du förstår följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
1. Så här [utforma återställningsnätverk](site-recovery-network-design.md)
1. [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
1. [Gör en redundansväxling till Azure](site-recovery-failover.md)
1. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
1. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Mönster för distribution

En Citrix XenApp och XenDesktop-servergruppen har vanligtvis följande distribution mönster:

**Mönster för distribution**

Citrix XenApp och XenDesktop-distributionen med AD DNS-server, SQL database server, Citrix Delivery Controller, StoreFront-server, XenApp Master (VDA), Citrix XenApp-licensservern

![Mönster för distribution 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

I den här artikeln, Citrix-distributioner på virtuella VMware-datorer som hanteras av vSphere 6.0 / System Center VMM 2012 R2 som användes för att konfigurera DR.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Inte i omfånget | Ja
**VMware** | Inte i omfånget | Ja
**Fysisk server** | Inte i omfånget | Ja

### <a name="versions"></a>Versioner
Kunder kan distribuera XenApp komponenter som virtuella datorer som körs på Hyper-V eller VMware eller fysiska servrar. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till Azure.
Eftersom XenApp 7,7 eller senare stöds i Azure, kan endast distributioner med dessa versioner redundansväxlas till Azure för Haveriberedskap och migrering.

### <a name="things-to-keep-in-mind"></a>Saker att tänka på

1. Skydd och återställning av lokala distributioner som använder Server OS datorer för att leverera XenApp appar som har publicerats och XenApp publiceras stationära datorer stöds.

2. Skydd och återställning av lokala distributioner med hjälp av fjärrskrivbord OS-datorer för att leverera Desktop VDI för klientens virtuella skrivbord, inklusive Windows 10, stöds inte. Det beror på att Site Recovery inte stöder återställning av datorer med desktop OS'es.  Dessutom vissa virtuella skrivbord klientoperativsystem (t.ex.) Windows 7) stöds inte ännu för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

3.  Azure Site Recovery kan inte replikera och skydda befintliga lokala MCS eller PVS kloner.
Du måste återskapa dessa kloner med hjälp av Azure RM etablering från domänkontrollant.

4. NetScaler kan inte skyddas med Azure Site Recovery som NetScaler baseras på FreeBSD och Azure Site Recovery stöder inte skydd av FreeBSD OS. Du skulle behöva distribuera och konfigurera en ny NetScaler-installation från Azure-marknadsplatsen efter redundansväxlingen till Azure.


## <a name="replicating-virtual-machines"></a>Replikering av virtuella datorer

Följande komponenter från Citrix XenApp-distributionen måste skyddas för att aktivera replikering och återställning.

* Skydda AD DNS-server
* Skydd av SQL database-server
* Skyddet av Citrix Delivery Controller
* Skydd av StoreFront-server.
* Protection för XenApp Master (VDA)
* Skyddet av Citrix XenApp-licensservern


**Replikering av AD-DNS-server**

Se [skydda Active Directory och DNS med Azure Site Recovery](site-recovery-active-directory.md) på riktlinjer för att replikera och konfigurera en domänkontrollant i Azure.

**SQL database Server-replikering**

Se [skydda SQL Server med SQL Server-haveriberedskap och Azure Site Recovery](site-recovery-sql.md) för detaljerad teknisk information om alternativ som rekommenderas för att skydda SQL-servrar.

Följ [den här vägledningen](site-recovery-vmware-to-azure.md) att börja replikera de andra komponenten virtuella datorerna till Azure.

![Protection för XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Beräknings- och nätverksinställningar**

När datorerna är skyddade (status visas som ”skyddad” under replikerade objekt), inställningarna för beräkning och nätverk måste konfigureras.
I beräkning och nätverk > beräkna egenskaper, du kan ange namnet och storleken för virtuell dator i Azure.
Ändra namnet till uppfyller kraven för Azure om du behöver. Du kan också visa och lägga till information om målnätverket, undernätet och IP-adress som ska tilldelas den virtuella Azure-datorn.

Observera följande:

* Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig under redundansväxlingen fungerar inte för växling vid fel. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.

* För AD/DNS-servern kan behålla en lokal adress du ange samma adress som DNS-server för det virtuella Azure-nätverket.

Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

*   Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
*   Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
* Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.
*   Om den virtuella datorn har flera nätverkskort ansluts alla till samma nätverk.
*   Om den virtuella datorn har flera nätverkskort, blir den första som visas i listan över standard-nätverkskort i Azure-dator.


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

När replikering har aktiverats för de virtuella datorerna komponenten XenApp, är nästa steg att skapa en återställningsplan.
En återställning planera grupper tillsammans virtuella datorer med liknande krav för redundans och återställning.  

**Steg för att skapa en återställningsplan**

1. Lägg till XenApp komponenten virtuella datorer i Recovery Plan.
2. Klicka på Återställningsplaner -> + Återställningsplanen. Ange en intuitiv namn för återställningsplanen.
3. För virtuella VMware-datorer: Välj källa som VMware processervern, mål som Microsoft Azure och distributionsmodell som Resource Manager och klicka på Välj objekt.
4. För Hyper-V-datorer: Välj källa som VMM-servern, mål som Microsoft Azure och som Resource Manager-distributionsmodellen och klicka på Välj objekt och välj sedan XenApp distributionen virtuella datorer.

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer i grupper för växling vid fel

Återställningsplaner kan anpassas för att lägga till redundansgrupper för specifika startordningen, skript och manuella åtgärder. Följande grupper måste läggas till i återställningsplanen.

1. Redundans Group1: AD DNS
2. Redundans Grupp2: Virtuella SQL Server-datorer
2. Redundans Group3: VDA Master bild VM
3. Redundans grupp4: Domänkontrollant och StoreFront server-datorer


### <a name="adding-scripts-to-the-recovery-plan"></a>Lägga till skript i en återställningsplan

Skript kan köras före eller efter en viss grupp i en återställningsplan. Manuella åtgärder kan också ingå och utförs under redundans.

Anpassade återställningsplanen ser ut som den nedan:

1. Redundans Group1: AD DNS
2. Redundans Grupp2: Virtuella SQL Server-datorer
3. Redundans Group3: VDA Master bild VM

   >[!NOTE]     
   >Steg 4, 6 och 7 som innehåller instruktioner för manuell eller skript som kan användas för endast en lokal XenApp > miljö med MCS/PVS kataloger.

4. Grupp 3 manuell eller skript åtgärd: Stänga av master VDA VM.
Master VDA VM vid redundansväxling till Azure kommer att körs. Om du vill skapa den nya MCS kataloger med Azure som är värd för master VDA VM måste vara stoppad (de allokerade) tillstånd. Stäng av den virtuella datorn från Azure-portalen.

5. Redundans grupp4: Domänkontrollant och StoreFront server-datorer
6. Group3 manuell eller skript åtgärd 1:

    ***Lägg till Azure RM-värd-anslutning***

    Skapa Azure-värd-anslutning i Delivery Controller-datorn för att etablera nya MCS-kataloger i Azure. Följ stegen som beskrivs i det här [artikeln](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3 manuell eller skript åtgärd 2:

    ***Återskapa MCS kataloger i Azure***

    Befintliga MCS eller PVS kloner på den primära platsen replikeras inte till Azure. Du måste återskapa dessa kloner med hjälp av replikerade master VDA och Azure etablering från domänkontrollant. Följ stegen som beskrivs i det här [artikeln](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) att skapa MCS kataloger i Azure.

![Återställningsplan för XenApp-komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Du kan använda skript på [plats](https://github.com/Azure/azure-quickstart-templates/tree/master/asr-automation-recovery/scripts) att uppdatera DNS med den nya IP-adresser för den över > virtuella datorer eller för att koppla en belastningsutjämnare på den redundansväxlade virtuella datorn, om det behövs.


## <a name="doing-a-test-failover"></a>Gör ett redundanstest

Följ [den här vägledningen](site-recovery-test-failover-to-azure.md) att göra ett redundanstest.

![Återställningsplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Gör en redundansväxling

Följ [den här vägledningen](site-recovery-failover.md) när du gör en redundansväxling.

## <a name="next-steps"></a>Nästa steg

Du kan [mer](https://aka.ms/citrix-xenapp-xendesktop-with-asr) om replikering av Citrix XenApp och XenDesktop-distributioner i detta dokument. Titta på för att [replikera andra program](site-recovery-workload.md) med Site Recovery.
