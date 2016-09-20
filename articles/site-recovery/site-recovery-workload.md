<properties
    pageTitle="Vilka arbetsbelastningar kan jag skydda med Azure Site Recovery?" 
    description="Azure Site Recovery skyddar dina arbetsbelastningar och program genom att samordna replikering, redundans och återställning av lokala virtuella datorer och fysiska servrar till Azure eller till en sekundär lokal plats" 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="07/06/2016" 
    ms.author="raynew"/>

# Vilka arbetsbelastningar kan jag skydda med Azure Site Recovery?


Den här artikeln beskriver vilka arbetsbelastningar och program som du kan skydda med Azure Site Recovery.

Skriv dina kommentarer eller frågor längst ned i den här artikeln eller i [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Översikt

Organisationer behöver en BCDR-strategi för affärskontinuitet och haveriberedskap som definierar hur appar, arbetsbelastningar och data är tillgängliga under planerade och oplanerade avbrott och hur de kan återställas till normalt drifttillstånd så fort som möjligt.

Tjänsten Site Recovery i Azure stödjer din BCDR-strategi och hjälper dig att distribuera programmedvetna återhämtningslösningar genom att samordna replikeringen av lokala fysiska servrar och virtuella datorer till molnet (Azure) eller till en sekundär plats. Oavsett om dina appar är Windows- eller Linux-baserade, körs på fysiska servrar eller om de är virtuella VMware- eller Hyper-V-datorer kan du använda Site Recovery för att hantera replikering, utföra haveriberedskapstester, köra redundansväxlingar och återställning efter fel.


Site Recovery integrerar med Microsoft-program som SharePoint, Exchange, Dynamics, SQL Server och Active Directory. Microsoft arbetar också nära andra branschledare som Oracle, SAP, IBM och Red Hat för att säkerställa att deras program och tjänster fungerar smidigt på Microsofts plattformar, inklusive Azure. Du kan anpassa dina lösningar för varje app.

## Varför ska jag använda Site Recovery för programskydd?

Site Recovery erbjuder skydd och återställning på programnivå enligt följande: 

- Nästintill synkron replikering med återställningspunktsmål så låga som 30 sekunder för att uppfylla kraven för de mest kritiska affärsapparna.
- Appkonsekventa ögonblicksbilder för program med en eller flera nivåer.
- Integrering med SQL Server AlwaysOn och samverkan med andra replikeringstekniker på programnivå som AD-replikering, SQL AlwaysOn, Exchange-databastillgänglighetsgrupper (DAG, Database Availability Group) och Oracle Data Guard.
- Flexibla återställningsplaner som gör att du kan återställa en hel programstack med en enda klickning och ta med externa skript och manuella åtgärder i planen. 
- Avancerad nätverkshantering i Site Recovery och Azure för att förenkla nätverkskraven för appar, inklusive möjligheten att reservera IP-adresser, konfigurera belastningsutjämning och integrera med Azure Traffic Manager för låga RTO-nätverksväxlingar.
-  Ett omfattande automationsbibliotek som ger tillgång till produktionsklara, programspecifika skript som kan ladas ned och integreras med återställningsplaner.



##Översikt över arbetsbelastningar

Site Recovery kan replikera appar som körs på en virtuell dator som stöds. Dessutom samarbetar vi med produktteam för att utföra ytterligare appspecifika tester.

**Arbetsbelastning** | **Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera virtuella Hyper-V-datorer till Azure** | **Replikera virtuella VMware-datorer till en sekundär plats** | **Replikera virtuella VMware-datorer till Azure**
---|---|---|---|---
Active Directory, DNS | Y | Y | Y | Y 
Webbappar (IIS, SQL) | Y | Y | Y | Y
SCOM | Y | Y | Y | Y
Sharepoint | Y | Y | Y | Y
SAP<br/><br/>Replikera SAP-plats till Azure för icke-kluster | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft)
Exchange (icke-DAG) | Y | Kommer snart | Y | Y
Fjärrskrivbord/VDI | Y | Y | Y | Saknas 
Linux (operativsystem och appar) | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft) 
Dynamics AX | Y | Y | Y | Y
Dynamics CRM | Y | Kommer snart | Y | Kommer snart
Oracle | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft) | Y (har testats av Microsoft)
Windows-filserver | Y | Y | Y | Y


## Replikera Active Directory och DNS

En Active Directory- och DNS-infrastruktur är fundamentalt för de flesta företagsappar. Under haveriberedskap måste du skydda och återställa dessa infrastrukturkomponenter innan du återställer dina arbetsbelastningar och appar.

Du kan använda Site Recovery för att skapa en fullständig automatiserad haveriberedskapsplan för Active Directory och DNS. Om du till exempel vill redundansväxla SharePoint och SAP från en primär till en sekundär plats kan du först konfigurera en återställningsplan som växlar över Active Directory och sedan ytterligare en appspecifik plan som växlar över de andra apparna som är beroende av Active Directory.

[Lär dig mer](site-recovery-active-directory.md) om hur du skyddar Active Directory och DNS.

## Skydda SQL Server

SQL Server utgör grunden för datatjänster i många affärsappar i ett lokalt datacenter.  Site Recovery kan användas tillsammans med HR/DR-tekniker för SQL Server för att skydda företagsappar med flera nivåer som använder SQL Server. Site Recovery tillhandahåller:

- En enkel och kostnadseffektiv haveriberedskapslösning för SQL Server. Replikera flera versioner och utgåvor av fristående SQL Server-servrar och SQL Server-kluster till Azure eller till en sekundär plats.  
- Integrering med SQL AlwaysOn-tillgänglighetsgrupper för att hantera redundans och återställning efter fel med Azure Site Recovery-återställningsplaner.
- Slutpunkt till slutpunkt-återställningsplaner för alla nivåer i ett program, inklusive SQL Server-databaser.
- Skalning av SQL Server vid hög arbetsbelastning med Site Recovery genom burst-överföring till större virtuella IaaS-datorstorlekar i Azure.
- Enkel testning av SQL Server-haveriberedskap. Du kan köra redundanstester för att analysera och köra efterlevnadskontroller utan att påverka produktionsmiljön.

[Lär dig mer](site-recovery-sql.md) om hur du skyddar SQL Server.

##Skydda SharePoint

Azure Site Recovery hjälper dig att skydda SharePoint-distributioner på följande sätt:

- Eliminerar behovet av och associerade infrastrukturkostnader för en reservservergrupp för haveriberedskap. Använd Site Recovery för att replikera en hel servergrupp (webb-, app- och databasnivåer) till Azure eller till en sekundär plats.
- Förenklar programdistribution och programhantering. Uppdateringar som distribueras till den primära platsen replikeras automatiskt och är därför tillgängliga efter redundansväxlingen av en servergrupp på en sekundär plats. Minskar också hanteringskomplexiteten och kostnaderna för att hålla en reservservergrupp uppdaterad.
- Förenklar utvecklingen och testningen av SharePoint-program genom att på begäran skapa en produktionslik kopia av replikmiljön för testning och felsökning.
- Förenklar övergången till molnet genom att använda Site Recovery för att migrera SharePoint-distributioner till Azure.

[Lär dig mer](https://gallery.technet.microsoft.com/SharePoint-DR-Solution-f6b4aeae) om hur du kan skydda SharePoint.


## Skydda Dynamics AX

Azure Site Recovery hjälper dig att skydda din Dynamics AX-baserade ERP-lösning genom att:

- Samordna replikeringen av hela Dynamics AX-miljön (webb- och AOS-nivåer, databasnivåer, SharePoint) till Azure eller till en sekundär plats.
- Förenkla migreringen av Dynamics AX-distributioner till molnet (Azure).
- Förenkla utvecklingen och testningen av Dynamics AX-program genom att på begäran skapa en produktionslik kopia för testning och felsökning.

[Lär dig mer](https://gallery.technet.microsoft.com/Dynamics-AX-DR-Solution-b2a76281) om hur du kan skydda Dynamic AX.

## Skydda Fjärrskrivbordstjänster 

Fjärrskrivbordstjänster (RDS) stöder VDI-sessionsbaserade (Virtual Desktop Infrastructure) skrivbord och program så att användarna kan arbeta var som helst. Med Azure Site Recovery kan du:

- Replikera hanterade eller ohanterade poolindelade virtuella skrivbord till en sekundär plats, och fjärrprogram och fjärrsessioner till en sekundär plats eller Azure.
- Du kan replikera det här:

**Fjärrskrivbordstjänster** | **Replikera virtuella Hyper-V-datorer till en sekundär plats** | **Replikera virtuella Hyper-V-datorer till Azure** | **Replikera virtuella VMware-datorer till en sekundär plats** | **Replikera virtuella VMware-datorer till Azure** | **Replikera fysiska servrar till en sekundär plats** | **Replikera fysiska servrar till Azure**
---|---|---|---|---|---|---
**Poolindelade virtuella skrivbord (ej hanterade)** | Ja | Nej | Ja | Nej | Ja | Nej
**Poolindelade virtuella skrivbord (hanterade och utan UPD)** | Ja | Nej | Ja | Nej | Ja | Nej
**Fjärrprogram och fjärrskrivbordssessioner (utan UPD)** | Ja | Ja | Ja | Ja | Ja | Ja


[Lär dig mer](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) om hur du kan skydda Fjärrskrivbordstjänster.


## Skydda Exchange

Site Recovery skyddar Exchange på följande sätt:

- För små Exchange-distributioner, till exempel en enskild server eller icke-klustrade servrar, kan Site Recovery replikera och redundansväxla till Azure eller till en sekundär plats.
- För större distributioner integrerar Site Recovery med Exchange-databastillgänglighetsgrupper (DAG).
- Exchange-databastillgänglighetsgrupper är den rekommenderade lösningen för haveriberedskap på större företag.  Site Recovery-återställningsplaner kan innehålla databastillgänglighetsgrupper för samordning av redundansväxlingen av dessa grupper mellan platser.


[Lär dig mer](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) om hur du kan skydda Exchange.

## Skydda SAP

Använd Site Recovery för att skydda din SAP-distribution på följande sätt: 

- Aktivera skydd för hela SAP-distributionen genom att replikera olika distributionslager till Azure eller till en sekundär plats.
- Förenkla molnmigreringen genom att använda Site Recovery för att migrera din SAP-distribution till Azure.
- Förenkla utvecklingen och testningen av SAP genom att skapa en produktionslik kopia på begäran för att testa och felsöka program.

[Lär dig mer](http://aka.ms/asr-sap) om hur du kan skydda SAP.

## Nästa steg

[Förbereda](site-recovery-best-practices.md) för Site Recovery-distribution




<!--HONumber=sep16_HO1-->


