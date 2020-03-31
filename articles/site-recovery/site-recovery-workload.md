---
title: Om haveriberedskap för lokala appar med Azure Site Recovery
description: Beskriver de arbetsbelastningar som kan skyddas med haveriberedskap med Azure Site Recovery-tjänsten.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062841"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Om haveriberedskap för lokala appar

I den här artikeln beskrivs lokala arbetsbelastningar och appar som du kan skydda för haveriberedskap med [Azure Site Recovery-tjänsten.](site-recovery-overview.md)

## <a name="overview"></a>Översikt

Organisationer behöver en strategi för affärskontinuitet och haveriberedskap (BCDR) för att hålla arbetsbelastningar och data säkra och tillgängliga under planerade och oplanerade driftstopp. Och återhämta sig till vanliga arbetsförhållanden.

Site Recovery är en Azure-tjänst som bidrar till din BCDR-strategi. Med Site Recovery kan du distribuera programmedveten replikering till molnet eller till en sekundär plats. Du kan använda Site Recovery för att hantera replikering, utföra haveriberedskapstestning och köra redundans och återställning efter fel. Dina appar kan köras på Datorer med Windows eller Linux, fysiska servrar, VMware eller Hyper-V.

Site Recovery integreras med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Microsoft har ett nära samarbete med ledande leverantörer, inklusive Oracle, SAP och Red Hat. Du kan anpassa replikeringslösningar på appbasis.

## <a name="why-use-site-recovery-for-application-replication"></a>Varför ska man använda Site Recovery för programreplikering?

Site Recovery erbjuder skydd och återställning på programnivå enligt följande:

- Appagnostiker och tillhandahåller replikering för alla arbetsbelastningar som körs på en dator som stöds.
- Nästan synkron replikering, med återställningspunktmål (RPO) så lågt som 30 sekunder för att möta behoven hos de mest kritiska affärsapparna.
- Appkonsekventa ögonblicksbilder för program med en eller flera nivåer.
- Integrering med SQL Server AlwaysOn och partnerskap med andra replikeringstekniker på programnivå. Till exempel Active Directory-replikering, SQL AlwaysOn och Exchange Database Availability Groups (DAGs).
- Flexibla återställningsplaner som gör att du kan återställa en hel programstack med ett enda klick och inkludera externa skript och manuella åtgärder i planen.
- Avancerad nätverkshantering i Site Recovery och Azure för att förenkla kraven i appnätverket. Nätverkshantering, till exempel möjligheten att reservera IP-adresser, konfigurera belastningsutjämning och integrering med Azure Traffic Manager för RTO-nätverksövergångar (Low Recovery Time).
- Ett omfattande automationsbibliotek som ger tillgång till produktionsklara, programspecifika skript som kan ladas ned och integreras med återställningsplaner.

## <a name="workload-summary"></a>Översikt över arbetsbelastningar

Site Recovery kan replikera alla appar som körs på en dator som stöds. Vi samarbetar med produktteam för att göra ytterligare tester för de appar som anges i följande tabell.

| **Arbetsbelastning** |**Replikera virtuella Azure-datorer till Azure** |**Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera virtuella virtuella datorer med VMware till Azure** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Ja |Ja |Ja |Ja |Ja|
| Webbappar (IIS, SQL) |Ja |Ja |Ja |Ja |Ja|
| System Center Operations Manager |Ja |Ja |Ja |Ja |Ja|
| SharePoint |Ja |Ja |Ja |Ja |Ja|
| SAP<br/><br/>Replikera en SAP-plats till Azure för icke-kluster |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft)|
| Exchange (icke-DAG) |Ja |Ja |Ja |Ja |Ja|
| Fjärrskrivbord/VDI |Ja |Ja |Ja |Ja |Ja|
| Linux (operativsystem och appar) |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft) |Ja (testad av Microsoft)|
| Dynamics AX |Ja |Ja |Ja |Ja |Ja|
| Windows-filserver |Ja |Ja |Ja |Ja |Ja|
| Citrix XenApp och XenDesktop |Ja|Ej tillämpligt |Ja |Ej tillämpligt |Ja |

## <a name="replicate-active-directory-and-dns"></a>Replikera Active Directory och DNS

En Active Directory- och DNS-infrastruktur är fundamentalt för de flesta företagsappar. Under haveriberedskap måste du skydda och återställa dessa infrastrukturkomponenter innan du återställer arbetsbelastningar och appar.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory och DNS. Om du till exempel vill växla över SharePoint och SAP från en primär till en sekundär plats kan du ställa in en återställningsplan som först misslyckas över Active Directory. Använd sedan ytterligare en appspecifik återställningsplan för att växla över de andra apparna som är beroende av Active Directory.

[Läs mer](site-recovery-active-directory.md) om haveriberedskap för Active Directory och DNS.

## <a name="protect-sql-server"></a>Skydda SQL Server

SQL Server tillhandahåller en stiftelse för datatjänster för många affärsappar i ett lokalt datacenter. Site Recovery kan användas med SQL Server HA/DR-teknik för att skydda företagsappar med flera nivåer som använder SQL Server.

Site Recovery tillhandahåller:

- En enkel och kostnadseffektiv haveriberedskapslösning för SQL Server. Replikera flera versioner och utgåvor av fristående SQL Server-servrar och SQL Server-kluster till Azure eller till en sekundär plats.
- Integrering med SQL AlwaysOn-tillgänglighetsgrupper för att hantera redundans och återställning efter fel med Azure Site Recovery-återställningsplaner.
- Slutpunkt till slutpunkt-återställningsplaner för alla nivåer i ett program, inklusive SQL Server-databaser.
- Skalning av SQL Server för toppbelastningar med Site Recovery, genom _att spränga_ dem i större IaaS virtuella datorstorlekar i Azure.
- Enkel testning av SQL Server-haveriberedskap. Du kan köra redundanstester för att analysera data och köra efterlevnadskontroller utan att påverka din produktionsmiljö.

[Läs mer](site-recovery-sql.md) om haveriberedskap för SQL-server.

## <a name="protect-sharepoint"></a>Skydda SharePoint

Azure Site Recovery hjälper dig att skydda SharePoint-distributioner på följande sätt:

- Eliminerar behovet av och associerade infrastrukturkostnader för en reservservergrupp för haveriberedskap. Använd Site Recovery för att replikera en hel servergrupp (webb-, app- och databasnivåer) till Azure eller till en sekundär plats.
- Förenklar programdistribution och programhantering. Uppdateringar som distribueras till den primära platsen replikeras automatiskt. Uppdateringarna är tillgängliga efter redundans och återställning av en servergrupp på en sekundär plats. Sänker ledningens komplexitet och kostnader i samband med att hålla en stand-by-gård uppdaterad.
- Förenklar utvecklingen och testningen av SharePoint-program genom att på begäran skapa en produktionslik kopia av replikmiljön för testning och felsökning.
- Förenklar övergången till molnet genom att använda Site Recovery för att migrera SharePoint-distributioner till Azure.

[Läs mer](site-recovery-sharepoint.md) om haveriberedskap för SharePoint.

## <a name="protect-dynamics-ax"></a>Skydda Dynamics AX

Azure Site Recovery hjälper dig att skydda din Dynamics AX ERP-lösning genom att:

- Hantera replikering av hela Dynamics AX-miljön (webb- och AOS-nivåer, databasnivåer, SharePoint) till Azure eller till en sekundär plats.
- Förenkla migreringen av Dynamics AX-distributioner till molnet (Azure).
- Förenkla utvecklingen och testningen av Dynamics AX-program genom att på begäran skapa en produktionslik kopia för testning och felsökning.

[Läs mer](site-recovery-dynamicsax.md) om haveriberedskap för Dynamisk AX.

## <a name="protect-remote-desktop-services"></a>Skydda fjärrskrivbordstjänster

Rds (Remote Desktop Services) möjliggör virtuell skrivbordsinfrastruktur (VDI), sessionsbaserade skrivbord och program som gör det möjligt för användare att arbeta var som helst.

Med Azure Site Recovery kan du replikera följande tjänster:

- Replikera hanterade eller ohanterade poolade virtuella skrivbord till en sekundär plats.
- Replikera fjärrprogram och sessioner till en sekundär plats eller Azure.

I följande tabell visas replikeringsalternativen:

| **RDS** |**Replikera virtuella Azure-datorer till Azure** | **Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera virtuella virtuella datorer med VMware till Azure** | **Replikera fysiska servrar till en sekundär plats** | **Replikera fysiska servrar till Azure** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Poolat virtuellt skrivbord (ohanterat)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Poolat virtuellt skrivbord (hanterat och utan UPD)** |Inga|Ja |Inga |Ja |Inga |Ja |Inga |
| **Fjärrprogram och skrivbordssessioner (utan UPD)** |Ja|Ja |Ja |Ja |Ja |Ja |Ja |

[Läs mer](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) om haveriberedskap för RDS.

## <a name="protect-exchange"></a>Skydda Exchange

Site Recovery skyddar Exchange på följande sätt:

- För små Exchange-distributioner, till exempel en enskild eller fristående server, kan Site Recovery replikera och redundansväxla till Azure eller till en sekundär plats.
- För större distributioner integrerar Site Recovery med Exchange DAG:ar.
- Exchange-databastillgänglighetsgrupper är den rekommenderade lösningen för haveriberedskap på större företag.  Site Recovery-återställningsplaner kan innehålla DAG:ar för att samordna DAG-redundans mellan platser.

Mer information om katastrofåterställning för Exchange finns i [Exchange DAGs](/Exchange/high-availability/database-availability-groups/database-availability-groups) och [Exchange disaster recovery](/Exchange/high-availability/disaster-recovery/disaster-recovery).

## <a name="protect-sap"></a>Skydda SAP

Använd Site Recovery för att skydda din SAP-distribution på följande sätt:

- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som körs lokalt genom att replikera komponenter till Azure.
- Aktivera skydd av SAP NetWeaver och icke-NetWeaver-produktionsprogram som kör Azure, genom att replikera komponenter till ett annat Azure-datacenter.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
- Förenkla SAP-projektuppgradering, testning och prototyper genom att skapa en produktionsklon på begäran för att testa SAP-program.

[Läs mer](site-recovery-sap.md) om haveriberedskap för SAP.

## <a name="protect-internet-information-services"></a>Skydda Internet informationstjänster

Använd Site Recovery för att skydda din IIS-distribution (Internet Information Services) enligt följande:

Azure Site Recovery erbjuder haveriberedskap genom att replikera viktiga komponenter i din miljö till en fjärrplats eller ett offentligt moln, till exempel Microsoft Azure. Eftersom de virtuella datorerna med webbservern och databasen replikeras till återställningsplatsen finns det inget krav på en separat säkerhetskopia för konfigurationsfiler eller certifikat. Programmappningar och bindningar som är beroende av miljövariabler som ändras efter redundans kan uppdateras via skript som är integrerade i haveriberedskapsplanerna. Virtuella datorer tas upp på återställningsplatsen endast under en redundans. Azure Site Recovery hjälper dig också att dirigera en heltäckande redundans genom att tillhandahålla följande funktioner:

- Användning av ordningsföljd för avstängning och start av virtuella datorer i olika nivåer.
- Lägga till skript för att tillåta uppdateringar av programberoenden och bindningar på de virtuella datorerna när de har startat. Skripten kan också användas för att uppdatera DNS-servern så att den pekar på återställningsplatsen.
- Allokera IP-adresser till virtuella datorer före redundans genom att mappa primära nätverk och återställningsnätverk och använda skript som inte behöver uppdateras efter redundans.
- Möjlighet till en redundansväxling med ett klick för flera webbprogram som eliminerar utrymmet för förvirring under en katastrof.
- Möjlighet att testa återställningsplaner i en isolerad miljö för DR-test.

[Läs mer](site-recovery-iis.md) om haveriberedskap för IIS.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>Skydda Citrix XenApp och XenDesktop

Använd Site Recovery för att skydda dina Citrix XenApp- och XenDesktop-distributioner på följande sätt:

- Aktivera skydd av Distributionen Av Citrix XenApp och XenDesktop. Replikera de olika distributionslagren till Azure: Active Directory, DNS-server, SQL-databasserver, Citrix Delivery Controller, StoreFront-server, XenApp Master (VDA), Citrix XenApp License Server.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din Citrix XenApp och XenDesktop SAP-distribution till Azure.
- Förenkla Citrix XenApp-/XenDesktop-testningen genom att skapa en produktionslik kopia på begäran för testning och felsökning.
- Den här lösningen gäller endast virtuella Windows Server-skrivbord och inte virtuella klientskrivbord. Virtuella klientskrivbord stöds ännu inte för licensiering i Azure. [Lär dig mer](https://azure.microsoft.com/pricing/licensing-faq/) om licensiering för klient/server-datorer i Azure.

[Läs mer](site-recovery-citrix-xenapp-and-xendesktop.md) om haveriberedskap för Citrix XenApp- och XenDesktop-distributioner. Eller så kan du hänvisa till [Citrix whitepaper](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>Nästa steg

[Läs mer](azure-to-azure-quickstart.md) om haveriberedskap för en virtuell Azure-dator.
