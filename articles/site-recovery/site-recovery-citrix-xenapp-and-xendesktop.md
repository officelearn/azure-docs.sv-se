---
title: "Replikera en flera nivåer Citrix XenDesktop XenApp distribution och använda Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du skyddar och återställer Citrix XenDesktop och XenApp distributioner med hjälp av Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: ponatara
ms.openlocfilehash: 52b123b598226e7b03ea9a31c40dd192fd76b191
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="replicate-a-multi-tier-citrix-xenapp-and-xendesktop-deployment-using-azure-site-recovery"></a>Replikera en flera nivåer Citrix XenApp XenDesktop distribution och använda Azure Site Recovery

## <a name="overview"></a>Översikt

Citrix XenDesktop är en lösning för virtualisering av Fjärrskrivbordstjänster som levererar skrivbord och program som en tjänst för ondemand-användare var som helst. Med FlexCast leverans teknik kan XenDesktop snabbt och säkert leverera program och skrivbord till användare.
Idag, tillhandahåller Citrix XenApp inte en katastrof återställningsfunktioner.

En bra lösning för haveriberedskap, ska tillåta modellering av återställningsplaner runt ovanstående programarkitekturer för komplexa och har också möjlighet att lägga till anpassade steg för att hantera mappning mellan olika nivåer som därför att tillhandahålla en enkel klickning att lösningen vid en katastrof som leder till en lägre Återställningstidsmål.

Det här dokumentet innehåller stegvisa anvisningar för att skapa en lösning för katastrofåterställning för dina lokala Citrix XenApp distributioner på Hyper-V och VMware vSphere-plattformar. Det här dokumentet beskriver också hur du utför ett redundanstest (disaster recovery-test) och en oplanerad redundansväxling till Azure med hjälp av återställningsplaner, konfigurationer som stöds och förutsättningar.


## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du känna till följande:

1. [Replikera en virtuell dator till Azure](site-recovery-vmware-to-azure.md)
1. Så här [utforma ett nätverk för återställning](site-recovery-network-design.md)
1. [Gör ett redundanstest till Azure](site-recovery-test-failover-to-azure.md)
1. [Gör en redundansväxling till Azure](site-recovery-failover.md)
1. Så här [replikera en domänkontrollant](site-recovery-active-directory.md)
1. Så här [replikera SQL Server](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Mönster för distribution

En grupp med Citrix XenApp och XenDesktop har vanligtvis följande distribution mönster:

**Mönster för distribution**

Citrix XenApp och XenDesktop distribution med AD DNS-server, SQL databasserver server, Citrix leverans styrenhet, StoreFront, XenApp Master (VDA), Citrix XenApp licensserver

![Distribution av mönstret 1](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-deployment.png)


## <a name="site-recovery-support"></a>Site Recovery-stöd

Citrix-distributioner på virtuella VMware-datorer som hanteras av vSphere 6.0 eller System Center VMM 2012 R2 som användes för att konfigurera DR för den här artikeln.

### <a name="source-and-target"></a>Källa och mål

**Scenario** | **Till en sekundär plats** | **Till Azure**
--- | --- | ---
**Hyper-V** | Inte i omfånget | Ja
**VMware** | Inte i omfånget | Ja
**Fysisk server** | Inte i omfånget | Ja

### <a name="versions"></a>Versioner
Kunderna kan distribuera XenApp komponenter som virtuella datorer på Hyper-V- eller VMware eller fysiska servrar. Azure Site Recovery kan skydda både fysiska och virtuella distributioner till Azure.
Eftersom XenApp 7,7 eller senare stöds i Azure, kan endast installationer med dessa versioner flyttas över till Azure för återställning eller migrering.

### <a name="things-to-keep-in-mind"></a>Saker att tänka på

1. Skydd och återställning av lokala distributioner med hjälp av Server-OS datorer att leverera XenApp publicerade appar och XenApp publiceras skrivbord stöds.

2. Skydd och återställning av lokala distributioner med hjälp av fjärrskrivbord OS-datorer för att leverera skrivbord VDI för klienten virtuella skrivbord, inklusive Windows 10 stöds inte. Det beror på att ASR inte stöder återställning av datorer med desktop OS'es.  Dessutom vissa virtuella skrivbord klientoperativsystem (t.ex. Windows 7) ännu stöds inte för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

3.  Azure Site Recovery kan replikera och skydda befintliga lokala MCS eller PVS kloningar.
Du måste omskapa dessa kloner med hjälp av Azure RM etablering från leverans domänkontrollant.

4. NetScaler kan inte skyddas med Azure Site Recovery som NetScaler baseras på FreeBSD och Azure Site Recovery stöder inte skydd av FreeBSD-OS. Du behöver att distribuera och konfigurera en ny installation NetScaler från Azure-marknadsplatsen efter en redundansväxling till Azure.


## <a name="replicating-virtual-machines"></a>Replikering av virtuella datorer

Följande komponenter från Citrix XenApp distributionen måste skyddas för att aktivera replikering och återställning.

* Skydd av AD-DNS-server
* Skydd av SQL-databasserver
* Skydd av Citrix leverans av domänkontrollant
* Skydd av StoreFront server.
* Skydd av XenApp Master (VDA)
* Skydd av Citrix XenApp licensserver


**AD DNS-server-replikering**

Se [skydda Active Directory och DNS med Azure Site Recovery](site-recovery-active-directory.md) på anvisningar för att replikera och konfigurerar en domänkontrollant i Azure.

**SQL Server databasreplikering**

Se [skydda SQL Server med SQL Server-katastrofåterställning och Azure Site Recovery](site-recovery-sql.md) detaljerad teknisk information om rekommenderade alternativ för att skydda SQL-servrar.

Följ [vägledningen](site-recovery-vmware-to-azure.md) att starta replikering av de andra komponenten virtuella datorerna till Azure.

![Skydd av XenApp komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-enablereplication.png)

**Beräknings- och nätverksinställningar**

När datorerna som är skyddade (status visas som ”skyddad” under replikerade objekt), inställningar för beräknings- och nätverksinställningarna måste konfigureras.
I beräknings- och nätverksinställningar > beräkna egenskaper, du kan ange den namnet och storleken på virtuella Azure-datorn.
Ändra namnet som ska uppfylla kraven för Azure om du behöver. Du kan också visa och lägga till information om målnätverket, undernätet och IP-adress som ska tilldelas till den virtuella Azure-datorn.

Observera följande:

* Du kan ange IP-måladressen. Om du inte anger någon adress använder den redundansväxlade datorn DHCP. Om du anger en adress som inte är tillgänglig under växling vid fel, fungerar inte för växling vid fel. Samma mål-IP-adress kan användas för att testa redundans om adressen är tillgänglig i nätverket för redundanstestet.

* För AD/DNS-servern kan behåller den lokala adressen du ange samma adress som DNS-server för det virtuella Azure-nätverket.

Antalet nätverkskort beror på storleken som du anger för den virtuella måldatorn enligt följande:

*   Om antalet nätverkskort på källdatorn är mindre än eller lika med antalet nätverkskort som tillåts för måldatorns storlek så kommer målet att ha samma antal kort som källan.
*   Om antalet nätverkskort för den virtuella källdatorn överskrider det tillåtna antalet för målstorleken så används den högsta målstorleken.
* Om en källdator exempelvis har två nätverkskort och måldatorn stöder fyra så kommer måldatorn att ha två kort. Om källdatorn har två nätverkskort men målstorleken endast stöder ett så kommer måldatorn bara att ha ett kort.
*   Om den virtuella datorn har flera nätverkskort ansluts alla till samma nätverk.
*   Om den virtuella datorn har flera nätverkskort, blir den första som visas i listan över standardnätverkskort i den virtuella Azure-datorn.


## <a name="creating-a-recovery-plan"></a>Skapa en återställningsplan

Efter att replikering har aktiverats för de virtuella datorerna komponenten XenApp, är nästa steg att skapa en återställningsplan.
En återställning planera grupper tillsammans virtuella datorer med samma krav för redundans och återställning.  

**Steg för att skapa en återställningsplan**

1. Lägg till XenApp komponenten virtuella datorer i Recovery planera.
2. Klicka på Återställningsplaner -> + Planera för återställning. Ange ett intuitivt namn för återställningsplanen.
3. För virtuella VMware-datorer: Välj källa som VMware processervern, mål som Microsoft Azure och distributionsmodell som Resource Manager och klicka på Välj objekt.
4. För Hyper-V virtuella datorer: Välj källa som VMM-servern, mål som Microsoft Azure och distributionsmodell som Resource Manager och klicka på Välj objekt och välj sedan distributionen XenApp virtuella datorer.

### <a name="adding-virtual-machines-to-failover-groups"></a>Lägga till virtuella datorer till grupper för växling vid fel

Återställningsplaner kan anpassas för att lägga till redundans grupper för specifika startordningen, skript och manuella åtgärder. Följande grupper måste läggas till i återställningsplanen.

1. Redundans Group1: AD DNS
2. Redundans Grupp2: SQL Server-datorer
2. Redundans Group3: VDA Master avbildningen VM
3. Failover Group4: Leverans styrenhet och StoreFront virtuella server-datorer


### <a name="adding-scripts-to-the-recovery-plan"></a>Att lägga till skript återställningsplanen

Skript kan köras före eller efter en viss grupp i en återställningsplan. Manuella åtgärder kan också ingå och utförs under växling vid fel.

Anpassade återställningsplanen ser ut som den nedan:

1. Redundans Group1: AD DNS
2. Redundans Grupp2: SQL Server-datorer
3. Redundans Group3: VDA Master avbildningen VM

   >[!NOTE]     
   >Steg 4, 6 och 7 som innehåller instruktioner för manuell eller skript som är tillämpliga på endast en lokal XenApp > miljö med MCS/PVS kataloger.

4. 3 manuell eller skript åtgärd: avstängning master VDA VM i Master VDA VM när växlas över till Azure kommer att vara i ett körningstillstånd. Om du vill skapa nya MCS kataloger med Azure ARM värd master VDA VM måste vara i Stoppat (de allokerade) tillstånd. Stäng den virtuella datorn från Azure-portalen.

5. Failover Group4: Leverans styrenhet och StoreFront virtuella server-datorer
6. Group3 manuell eller skript åtgärd 1:

    ***Lägg till Azure RM värdanslutning***

    Skapa Azure ARM-värdanslutning i leverans Controller datorn för att etablera nya MCS kataloger i Azure. Följ stegen som beskrivs i det här [artikel](https://www.citrix.com/blogs/2016/07/21/connecting-to-azure-resource-manager-in-xenapp-xendesktop/).

7. Group3 manuell eller skript åtgärd 2:

    ***Skapa nytt MCS kataloger i Azure***

    Befintliga MCS eller PVS kloner på den primära platsen replikeras inte till Azure. Du måste omskapa dessa kloner med hjälp av replikerade master VDA och Azure ARM etablering från leverans domänkontrollant. Följ stegen som beskrivs i det här [artikel](https://www.citrix.com/blogs/2016/09/12/using-xenapp-xendesktop-in-azure-resource-manager/) skapa MCS kataloger i Azure.

![Återställningsplan för XenApp komponenter](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-recoveryplan.png)


   >[!NOTE]
   >Du kan använda skript på [plats](https://github.com/Azure/azure-quickstart-templates/blob/>master/asr-automation-recovery/scripts) att uppdatera DNS med den nya IP-adresser för den över > virtuella datorer eller för att koppla en belastningsutjämnare på den över virtuella datorn, om det behövs.


## <a name="doing-a-test-failover"></a>Gör ett redundanstest

Följ [vägledningen](site-recovery-test-failover-to-azure.md) att göra ett redundanstest.

![Återställningsplan](./media/site-recovery-citrix-xenapp-and-xendesktop/citrix-tfo.png)


## <a name="doing-a-failover"></a>Genomför en redundansväxling enligt

Följ [vägledningen](site-recovery-failover.md) när du gör en redundansväxling.

## <a name="next-steps"></a>Nästa steg

Du kan [mer](https://aka.ms/citrix-xenapp-xendesktop-with-asr) om att replikera Citrix XenApp och XenDesktop distributioner i detta dokument. Titta på vägledning för att [replikera andra program](site-recovery-workload.md) med Site Recovery.
