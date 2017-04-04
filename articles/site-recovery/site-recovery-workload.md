---
title: Vilka arbetsbelastningar kan jag skydda med Azure Site Recovery?
description: "Azure Site Recovery skyddar dina arbetsbelastningar och program genom att samordna replikering, redundans och återställning av lokala virtuella datorer och fysiska servrar till Azure eller till en sekundär lokal plats"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 34a9d187eecec185e2b8d6977baea267ca9e60e5
ms.lasthandoff: 03/28/2017


---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Vilka arbetsbelastningar kan jag skydda med Azure Site Recovery?
Den här artikeln beskriver arbetsbelastningar och program som kan replikeras med Azure Site Recovery-tjänsten.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Översikt
Organisationer behöver en strategi för affärskontinuitet och haveriberedskap (BCDR) för att hålla arbetsbelastningar och data säker och tillgänglig vid planerade såväl som oplanerade avbrott, samt återställa dem till fungerande tillstånd så snabbt som möjligt.

Site Recovery är en Azure-tjänst som bidrar till din BCDR-strategi. Med Site Recovery kan du distribuera programmedveten replikering till molnet eller till en sekundär plats. Oavsett om dina appar är Windows- eller Linux-baserade, körs på fysiska servrar, VMware eller Hyper-V så kan du använda Site Recovery för att samordna replikering, utföra haveriberedskapstester, köra redundansväxlingar och återställning efter fel.

Site Recovery integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Microsoft är även i nära samarbete med ledande leverantörer som Oracle, SAP, IBM och Red Hat. Du kan anpassa replikeringslösningar på appbasis.

## <a name="why-use-site-recovery-for-application-replication"></a>Varför ska man använda Site Recovery för programreplikering?
Site Recovery erbjuder skydd och återställning på programnivå enligt följande:

* App-oberoende replikering för alla arbetsbelastningar som körs på en dator som stöds.
* Nästintill synkron replikering med RPO:er så låga som 30 sekunder, för att uppfylla kraven för de mest kritiska affärsapparna.
* Appkonsekventa ögonblicksbilder för program med en eller flera nivåer.
* Integrering med SQL Server AlwaysOn och samverkan med andra replikeringstekniker på programnivå som AD-replikering, SQL AlwaysOn, Exchange-databastillgänglighetsgrupper (DAG, Database Availability Group) och Oracle Data Guard.
* Flexibla återställningsplaner som gör att du kan återställa en hel programstack med ett enda klick och ta med externa skript och manuella åtgärder i planen.
* Avancerad nätverkshantering i Site Recovery och Azure för att förenkla nätverkskraven för appar, inklusive möjligheten att reservera IP-adresser, konfigurera belastningsutjämning och integrera med Azure Traffic Manager för låga RTO-nätverksväxlingar.
* Ett omfattande automationsbibliotek som ger tillgång till produktionsklara, programspecifika skript som kan ladas ned och integreras med återställningsplaner.

## <a name="workload-summary"></a>Översikt över arbetsbelastningar
Site Recovery kan replikera alla appar som körs på en dator som stöds. Dessutom samarbetar vi med produktteam för att utföra ytterligare appspecifika tester.

| **Arbetsbelastning** | **Replikera Hyper-V-VM:ar till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera VMware-VM:ar till Azure** |
| --- | --- | --- | --- | --- |
| Active Directory, DNS |Y |Y |Y |Y |
| Webbappar (IIS, SQL) |Y |Y |Y |Y |
| System Center Operations Manager |Y |Y |Y |Y |
| Sharepoint |Y |Y |Y |Y |
| SAP<br/><br/>Replikera en SAP-plats till Azure för icke-kluster |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |
| Exchange (icke-DAG) |Y |Kommer snart |Y |Y |
| Fjärrskrivbord/VDI |Y |Y |Y |Saknas |
| Linux (operativsystem och appar) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |
| Dynamics AX |Y |Y |Y |Y |
| Dynamics CRM |Y |Kommer snart |Y |Kommer snart |
| Oracle |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |Y (har testats av Microsoft) |
| Windows-filserver |Y |Y |Y |Y |

## <a name="replicate-active-directory-and-dns"></a>Replikera Active Directory och DNS
En Active Directory- och DNS-infrastruktur är fundamentalt för de flesta företagsappar. Vid haveriberedskap, behöver du skydda och återställa de här infrastrukturkomponenterna innan du återställer dina arbetsbelastningar och appar.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory och DNS. Om du till exempel vill redundansväxla SharePoint och SAP från en primär till en sekundär plats, kan du konfigurera en återställningsplan som först växlar över Active Directory och sedan en ytterligare, appspecifik plan som växlar över de andra apparna som är beroende av Active Directory.

[Lär dig mer](site-recovery-active-directory.md) om hur du skyddar Active Directory och DNS.

## <a name="protect-sql-server"></a>Skydda SQL Server
SQL Server utgör grunden för datatjänster i många affärsappar i ett lokalt datacenter.  Site Recovery kan användas tillsammans med HA/DR-teknologier för SQL Server för att skydda företagsappar med flera nivåer som använder SQL Server. Site Recovery tillhandahåller:

* En enkel och kostnadseffektiv haveriberedskapslösning för SQL Server. Replikera flera versioner och utgåvor av fristående SQL Server-servrar och SQL Server-kluster till Azure eller till en sekundär plats.  
* Integrering med SQL AlwaysOn-tillgänglighetsgrupper för att hantera redundans och återställning efter fel med Azure Site Recovery-återställningsplaner.
* Slutpunkt till slutpunkt-återställningsplaner för alla nivåer i ett program, inklusive SQL Server-databaser.
* Skalning av SQL Server för hög arbetsbelastning med Site Recovery genom att ”bursta” dem till större virtuella IaaS-datorstorlekar i Azure.
* Enkel testning av SQL Server-haveriberedskap. Du kan köra redundanstester för att analysera data och köra efterlevnadskontroller utan att påverka din produktionsmiljö.

[Lär dig mer](site-recovery-sql.md) om hur du skyddar SQL Server.

## <a name="protect-sharepoint"></a>Skydda SharePoint
Azure Site Recovery hjälper dig att skydda SharePoint-distributioner på följande sätt:

* Eliminerar behovet av och associerade infrastrukturkostnader för en reservservergrupp för haveriberedskap. Använd Site Recovery för att replikera en hel servergrupp (webb-, app- och databasnivåer) till Azure eller till en sekundär plats.
* Förenklar programdistribution och programhantering. Uppdateringar som distribueras till den primära platsen replikeras automatiskt och är därför tillgängliga efter redundansväxlingen av en servergrupp på en sekundär plats. Minskar också hanteringskomplexiteten och kostnaderna för att hålla en reservservergrupp uppdaterad.
* Förenklar utvecklingen och testningen av SharePoint-program genom att på begäran skapa en produktionslik kopia av replikmiljön för testning och felsökning.
* Förenklar övergången till molnet genom att använda Site Recovery för att migrera SharePoint-distributioner till Azure.

[Lär dig mer](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) om hur du kan skydda SharePoint.

## <a name="protect-dynamics-ax"></a>Skydda Dynamics AX
Azure Site Recovery hjälper dig att skydda din Dynamics AX ERP-lösning genom att:

* Samordna replikeringen av hela Dynamics AX-miljön (webb- och AOS-nivåer, databasnivåer, SharePoint) till Azure eller till en sekundär plats.
* Förenkla migreringen av Dynamics AX-distributioner till molnet (Azure).
* Förenkla utvecklingen och testningen av Dynamics AX-program genom att på begäran skapa en produktionslik kopia för testning och felsökning.

[Lär dig mer](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) om hur du kan skydda Dynamic AX.

## <a name="protect-rds"></a>Skydda Fjärrskrivbordstjänster
Fjärrskrivbordstjänster (RDS) stöder VDI-sessionsbaserade (Virtual Desktop Infrastructure) skrivbord och program så att användarna kan arbeta var som helst. Med Azure Site Recovery kan du:

* Replikera hanterade eller ohanterade poolindelade virtuella skrivbord till en sekundär plats, och fjärrprogram och fjärrsessioner till en sekundär plats eller Azure.
* Du kan replikera det här:

| **RDS** | **Replikera Hyper-V-VM:ar till en sekundär plats** | **Replikera Hyper-V-VM:ar till Azure** | **Replikera VMware-VM:ar till en sekundär plats** | **Replikera VMware-VM:ar till Azure** | **Replikera fysiska servrar till en sekundär plats** | **Replikera fysiska servrar till Azure** |
| --- | --- | --- | --- | --- | --- | --- |
| **Poolat virtuellt skrivbord (ohanterat)** |Ja |Nej |Ja |Nej |Ja |Nej |
| **Poolat virtuellt skrivbord (hanterat och utan UPD)** |Ja |Nej |Ja |Nej |Ja |Nej |
| **Fjärrprogram och skrivbordssessioner (utan UPD)** |Ja |Ja |Ja |Ja |Ja |Ja |

[Lär dig mer](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) om hur du kan skydda Fjärrskrivbordstjänster.

## <a name="protect-exchange"></a>Skydda Exchange
Site Recovery skyddar Exchange på följande sätt:

* För små Exchange-distributioner, till exempel en enskild eller fristående server, kan Site Recovery replikera och redundansväxla till Azure eller till en sekundär plats.
* För större distributioner integrerar Site Recovery med Exchange DAG:ar.
* Exchange-databastillgänglighetsgrupper är den rekommenderade lösningen för haveriberedskap på större företag.  Site Recovery-återställningsplaner kan innehålla DAG:ar för att samordna DAG-redundans mellan platser.

[Lär dig mer](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) om hur du kan skydda Exchange.

## <a name="protect-sap"></a>Skydda SAP
Använd Site Recovery för att skydda din SAP-distribution på följande sätt:

* Aktivera skydd för hela SAP-distributionen genom att replikera olika distributionslager till Azure eller till en sekundär plats.
* Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
* Förenkla SAP-utveckling och -testning genom att skapa en produktionslik kopia på begäran för testning och felsökning av program.

[Lär dig mer](http://aka.ms/asr-sap) om hur du kan skydda SAP.

## <a name="protect-iis"></a>Skydda IIS
Använd Site Recovery för att skydda din IIS-distribution på följande sätt:

Azure Site Recovery erbjuder haveriberedskap genom att replikera viktiga komponenter i din miljö till en fjärrplats eller ett offentligt moln, till exempel Microsoft Azure. Eftersom den virtuella datorn med webbservern och databasen replikeras till återställningsplatsen finns inget behov av separat säkerhetskopiering av konfigurationsfiler eller certifikat. Programmappningar och bindningar som är beroende av miljövariabler som ändras efter redundans kan uppdateras via skript som är integrerade i haveriberedskapsplanerna. Virtuella datorer finns endast på återställningsplatsen i händelse av redundans. Azure Site Recovery hjälper dig även att dirigera redundans från slutpunkt till slutpunkt genom att erbjuda följande funktioner:

-    Användning av ordningsföljd för avstängning och start av virtuella datorer i olika nivåer.
-    Tillägg av skript för att tillåta uppdatering av programberoenden och bindningar på de virtuella datorerna efter att de startats. Skripten kan också användas för att uppdatera DNS-servern så att den pekar på återställningsplatsen.
-    Allokering av IP-adresser till virtuella datorer före redundans genom mappning av det primära nätverket och återställningsnätverket. På så sätt används skript som inte behöver uppdateras efter redundans.
-    Möjlighet för redundans med ett klick för flera webbprogram på webbservrarna, vilket minskar risken för förväxling i händelse av ett haveri.
-    Möjlighet att testa återställningsplaner i en isolerad miljö för DR-test.

[Läs mer](https://aka.ms/asr-iis) om hur du skyddar en IIS-webbservergrupp.

## <a name="next-steps"></a>Nästa steg
[Kontrollera krav](site-recovery-prereq.md) 

