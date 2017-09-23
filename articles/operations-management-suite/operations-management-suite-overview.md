---
title: "Översikt över Operations Management Suite (OMS) | Microsoft Docs"
description: "Microsoft Operations Management Suite (OMS) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala och molnbaserade infrastruktur.  Den här artikeln beskriver värdet med OMS, identifierar olika tjänster och erbjudanden som ingår i OMS och innehåller länkar till detaljerat innehåll om tjänsterna."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 9dc437b9-e83c-45da-917c-cb4f4d8d6333
ms.service: operations-management-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 452dd602387db6db04ca87f6834c9e8606185484
ms.contentlocale: sv-se
ms.lasthandoff: 06/28/2017

---
# <a name="what-is-operations-management-suite-oms"></a>Vad är Operations Management Suite (OMS)?
Den här artikeln ger en introduktion till Operations Management Suite (OMS) med en kort översikt över affärsvärdet, tjänsterna, hanteringslösningarna samt paketerbjudanden med olika tjänster och lösningar.  Det finns också länkar till detaljerad dokumentation om hur du distribuerar och använder varje tjänst och lösning.

## <a name="from-on-premises-to-the-cloud"></a>Från lokalt till molnet
Microsoft har länge erbjudit produkter för att hantera företagsmiljöer.  2007 konsoliderades flera produkter i System Center-sviten.  Till exempel konsoliderades Configuration Manager med funktioner som distribution och inventering av programvara, Operations Manager med proaktiv övervakning av system och program, Orchestrator med runbook-flöden för att automatisera manuella processer och Data Protection Manager med säkerhetskopiering och återställning av viktiga data.

I och med att allt fler datorresurser flyttades till molnet utökades System Center med fler molnfunktioner för att hantera resurser i Azure, till exempel Operations Manager och Orchestrator.  Dessa var fortfarande utformade som lokala lösningar och krävde stora investeringar i distribution och underhåll av en lokal hanteringsmiljö.  För att dra full nytta av molnet och stödja framtidens program krävdes ett nytt tillvägagångssätt.

## <a name="introducing-operations-management-suite"></a>Operations Management Suite
Operations Management Suite (även kallat OMS) är en samling hanteringstjänster som redan från början har utformats för molnet.  I stället för att distribuera och hantera lokala resurser finns alla OMS-komponenter i Azure.  Konfigurationen är minimal, och du kommer igång på bara några minuter.  

- **Distribution med minimal kostnad och komplexitet.**  Eftersom alla OMS-komponenter och data lagras i Azure kommer du snabbt igång och behöver inte några komplexa och dyra lokala komponenter.
- **Skala till molnnivåer.**  Du behöver inte oroa dig för att du ska få betala för beräkningsresurser som du inte behöver eller att du ska få slut på lagringsutrymme. Med molnet betalar du endast för det du faktiskt använder och du kan skala direkt efter dina behov.  Börja med att hantera ett par resurser och skala sedan upp till hela din miljö.
- **Dra nytta av de senaste funktionerna.**  Nya och uppdaterade funktioner läggs hela tiden till i OMS-tjänsterna.  Du har alltid tillgång till de senaste funktionerna utan att du behöver distribuera uppdateringar.
- **Integrerade tjänster.**  Varje OMS-tjänst har ett eget affärsvärde, men de kan också samverka för att lösa komplexa scenarier.  En runbook i Azure Automation kan till exempel köra en redundansväxling med Azure Site Recovery och sedan logga information till Log Analytics för att generera en avisering.
- **Globala kunskaper.**  Hanteringslösningarna i OMS har löpande tillgång till den senaste informationen.  Säkerhets- och granskningslösningen kan till exempel utföra en hotanalys baserat på de senaste hoten som identifierats runt om i världen.
- **Åtkomst överallt.**  Få åtkomst till din hanteringsmiljö överallt via webbläsaren.  Installera OMS-appen på mobilen och få åtkomst till övervakningsdata.

### <a name="is-it-just-for-the-cloud"></a>Är det bara för molnet?
Bara för att OMS-tjänsterna körs i molnet betyder det inte att de inte kan hantera din lokala miljö på ett effektivt sätt.  Placera en agent i valfri Windows- eller Linux-dator i ditt datacenter så skickar den data till Log Analytics. I Log Analytics kan data analyseras tillsammans med alla andra data som samlats in från molntjänster eller lokala tjänster.  Med Azure Backup och Azure Site Recovery kan du utnyttja molnet för säkerhetskopiering och få hög tillgänglighet för lokala resurser.  
Runbook-flöden i molnet kan normalt inte komma åt dina lokala resurser, men du kan installera en agent på en eller flera datorer som fungerar som värd för runbook-flöden i ditt datacenter.  När du startar en runbook anger du om du vill att den ska köras i molnet eller på en lokal arbetsprocess.

## <a name="hybrid-management-with-system-center"></a>Hybridhantering med System Center
Om du har en befintlig installation av System Center kan du integrera dessa komponenter med OMS-tjänsterna och få en hybridlösning för både din lokala miljö och din molnmiljö. På så sätt kan du utnyttja fördelarna med varje produkt.  Anslut din befintliga Operations Manager-hanteringsgrupp till Log Analytics om du vill analysera hanterade agenter i molnet.  Och använd din befintliga säkerhetskopieringsprocess med Data Protection Manager för att säkerhetskopiera dina data till molnet.  


## <a name="oms-services"></a>OMS-tjänster
Huvudfunktionerna i OMS tillhandahålls genom en uppsättning tjänster som körs i Azure.  Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

|| Tjänst | Beskrivning |
|:--|:--|:--|
| ![Log Analytics](media/operations-management-suite-overview/icon-log-analytics.png) | Log Analytics | Övervaka och analysera tillgänglighet och prestanda för olika resurser inklusive fysiska och virtuella datorer. |
| ![Azure Automation](media/operations-management-suite-overview/icon-automation.png) | Automation | Automatisera manuella processer och tillämpa konfigurationer för fysiska och virtuella datorer. |
| ![Azure Backup](media/operations-management-suite-overview/icon-backup.png) | Säkerhetskopiering | Säkerhetskopiera och återställa kritiska data. |
| ![Azure Site Recovery](media/operations-management-suite-overview/icon-site-recovery.png) | Site Recovery | Ge hög tillgänglighet för viktiga program. |

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning för OMS genom att samla in data från hanterade resurser i en central databas.  Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.  Med den här metoden kan du konsolidera data från olika källor och kombinera data från dina Azure-tjänster med din befintliga lokala miljö.  Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.  

![Översikt över Log Analytics](media/operations-management-suite-overview/overview-log-analytics.png)

#### <a name="collecting-data"></a>Samla in data
Det finns en mängd olika sätt som du kan få data till databasen för Log Analytics för analys.

- **Windows- eller Linux-datorer och virtuella datorer.**  Du installerar Microsoft Monitoring Agent på de [Windows](../log-analytics/log-analytics-windows-agents.md)- och [Linux](../log-analytics/log-analytics-linux-agents.md)-datorer eller virtuella datorer som du vill samla in data från.  Från Log Analytics hämtar agenten automatiskt en konfiguration som definierar vilka händelse- och prestandadata som ska samlas in.  Du kan enkelt installera agenten på virtuella datorer som körs i Azure med Azure Portal.  Om du har en befintlig Operations Manager-miljö kan du ansluta hanteringsgruppen till Log Analytics och automatiskt börja samla in data från alla befintliga agenter.
- **Azure-tjänster.**  Log Analytics samlar in telemetri från [Azure Diagnostics och Azure Monitoring](../log-analytics/log-analytics-azure-storage.md) i databasen så att du kan övervaka Azure-resurser.
- **API för datainsamling.**  Log Analytics har ett [REST-API för att fylla i data från alla klienter](../log-analytics/log-analytics-data-collector-api.md).  Det gör att du kan samla in data från program från tredje part eller implementera anpassade scenarier.  En vanlig metod är att använda en runbook i Azure Automation för att samla in data och sedan använda API:t för datainsamling för att skriva dessa data till databasen.

#### <a name="reporting-and-analyzing-data"></a>Rapportera och analysera data
Log Analytics innehåller ett kraftfullt frågespråk för att extrahera data som lagras i databasen.  Eftersom data från alla källor lagras som poster kan du analysera data från flera källor med en enda fråga.
  
Förutom ad hoc-analyser tillhandahåller Log Analytics flera sätt att rapportera och analysera data från en fråga.

- **Vyer och instrumentpaneler.**  [Vyer](../log-analytics/log-analytics-view-designer.md) och [instrumentpaneler](../log-analytics/log-analytics-dashboards.md) visualiserar resultatet av en fråga i portalen.  Hanteringslösningar har vanligtvis vyer som analyserar data från lösningen.  Du kan också skapa egna anpassade vyer för att analysera data och göra dem lättillgängliga i din anpassade portal.
- **Exportera.**  Du kan välja att exportera resultatet av en fråga och analysera resultatet utanför Log Analytics.  Du kan även schemalägga en export till [Power BI](../log-analytics/log-analytics-powerbi.md) och få tillgång till omfattande funktioner för visualisering och analys.
- **Loggsöknings-API.**  Log Analytics har ett [REST-API för att samla in data från alla klienter](../log-analytics/log-analytics-log-search-api.md).  Det gör att du kan arbeta programmatiskt med data som samlats in i databasen eller komma åt dessa data från ett annat övervakningsverktyg.

#### <a name="alerting"></a>Aviseringar
Log Analytics kan skicka [proaktiva aviseringar](../log-analytics/log-analytics-alerts.md) till dig eller vidta åtgärder när ett problem upptäcks.  Precis som med alla andra analyser i Log Analytics utförs detta med en loggsökning.  Sökningen körs enligt ett regelbundet schema och en avisering skapas om resultatet uppfyller vissa villkor.

![Log Analytics-aviseringar](media/operations-management-suite-overview/overview-alerts.png)

Utöver att skapa en aviseringspost i Log Analytics-databasen kan följande åtgärder utföras med aviseringar.

- **E-post.**  Ett e-postmeddelande kan skickas för att informera dig om ett identifierat problem.
- **Runbook.**  En avisering i Log Analytics kan starta en runbook i Azure Automation.  Detta görs vanligtvis för att försöka åtgärda ett identifierat problem.  En runbook kan startas i molnet om det finns ett problem i Azure eller i något annat moln, eller så kan den startas på en lokal agent om problemet finns på en fysisk eller virtuell dator.
- **Webhook.**  En avisering kan starta en webhook och skicka data från resultatet av loggsökningen.  Detta möjliggör integrering med externa tjänster, till exempel ett alternativt aviseringssystem. Alternativt kan den försöka vidta åtgärder för en extern webbplats.

### <a name="azure-automation"></a>Azure Automation
[Azure Automation](http://azure.microsoft.com/documentation/services/automation) förser OMS med funktioner för processautomatisering och konfigurationshantering.  Med Azure Automation kan du automatisera manuella processer och tillämpa konfigurationer på fysiska och virtuella datorer.  

#### <a name="process-automation"></a>Processautomatisering
Azure Automation automatiserar manuella processer med hjälp av [runbook-flöden](../automation/automation-runbook-types.md) som är baserade på PowerShell-skript eller PowerShell-arbetsflöden.  Azure Automation innefattar också tillgångar som stöder runbook-flöden, till exempel variabler som kan delas mellan flera runbook-flöden och autentiseringsuppgifter och anslutningar som gör det möjligt att lagra krypterad information som kan krävas för en runbook (för autentisering).
Runbook-flöden erbjuder processautomatisering för andra tjänster i sviten.  Eftersom det går att komma åt var och en av de andra tjänsterna med PowerShell eller via ett REST-API kan du skapa runbook-flöden som utför funktioner som att samla in hanteringsdata i Log Analytics eller initiera en säkerhetskopiering med Azure Backup.

##### <a name="accessing-resources"></a>Komma åt resurser
Eftersom runbook-flöden baseras på PowerShell kan de hantera alla resurser som går att komma åt med PowerShell-cmdlets.  När du [läser in en modul](../automation/automation-integration-modules.md) i ditt Automation-konto blir den tillgängliga för alla runbook-flöden i det kontot. 
 
När runbook-flöden körs i molnet kan de komma åt alla resurser som är tillgängliga från molnet.  Det kan till exempel vara resurser i din Azure-prenumeration, i ett annat moln som Amazon Web Services (AWS) eller i en tjänst som kan nås via ett REST-API.  Autentiseringsuppgifter används inte för runbook-flöden i molnet, men runbook-flöden kan utnyttja Automation-tillgångar som till exempel autentiseringsuppgifter, anslutningar och certifikat för att autentisera de resurser som de har åtkomst till.

Resurser i ditt datacenter kan troligen inte nås från en runbook som körs i molnet.  Du kan installera en eller flera [Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md) i ditt datacenter genom för att köra runbook-flöden som behöver åtkomst till lokala resurser.  När du startar en runbook anger du om den ska köras i molnet eller på en specifik arbetsprocess.

![Runbook-flöden för Azure Automation](media/operations-management-suite-overview/overview-runbooks.png)

##### <a name="starting-a-runbook"></a>Starta en runbook
Runbook-flöden kan [startas på flera olika sätt](../automation/automation-starting-a-runbook.md) beroende på vad de ska användas till.  

- **Azure Portal.**  Precis som övriga Azure-tjänster kan även Azure Automation hanteras från Azure Portal.  Förutom att starta runbook-flöden kan du även importera eller skapa egna runbook-flöden.
- **Schemalägga.**  Du kan schemalägga runbook-flöden så att de startar med jämna mellanrum.  På så sätt kan du automatiskt upprepa en vanlig hanteringsprocess eller samla in data till Log Analytics.
- **PowerShell och API.**  Du kan starta runbook-flöden och ge dem nödvändig parameterinformation från en PowerShell-cmdlet eller REST-API:t för Azure Automation.  
- **Webhook.**  En webhook kan skapas för alla runbook-flöden som gör att den kan startas från externa program eller webbplatser.
- **Avisering i Log Analytics.**  En avisering i Log Analytics kan automatiskt starta en runbook för att försöka åtgärda ett identifierat problem.

#### <a name="configuration-management"></a>Konfigurationshantering
[PowerShell Desired State Configuration (DSC)](../automation/automation-dsc-overview.md) är en hanteringsplattform i Windows PowerShell som gör att du kan distribuera och tillämpa konfigurationen på fysiska och virtuella datorer.  Azure Automation hanterar DSC-konfigurationer och tillhandahåller en hämtningsserver i molnet som agenter kan använda för att hämta nödvändiga konfigurationer.

![Azure Automation DSC](media/operations-management-suite-overview/overview-dsc.png)

### <a name="azure-backup-and-azure-site-recovery"></a>Azure Backup och Azure Site Recovery
Azure Backup och Azure Site Recovery bidrar till verksamhetskontinuitet och haveriberedskap.  Båda tjänsterna har funktioner som hjälper dig att garantera att programmen är tillgängliga vid driftstörningar och att de återgår till normal drift när systemen är igång igen.  Båda tjänsterna hjälper till att uppfylla målen för återställningspunkt och återställningstid som definierats för din organisation. Ditt mål för återställningspunkt anger gränsen för hur länge data får vara otillgängliga under ett avbrott och ditt mål för återställningstid anger gränsen för hur länge en tjänst eller app får vara otillgänglig under ett avbrott.

#### <a name="azure-backup"></a>Azure Backup
[Azure Backup](http://azure.microsoft.com/documentation/services/backup) förser OMS med tjänster för säkerhetskopiering och återställning av data.  Tjänsten skyddar dina programdata och sparar dem i åratal utan stora investeringar och med minimala driftkostnader.  Funktionen kan säkerhetskopiera data från fysiska och virtuella Windows-servrar och programarbetsbelastningar som SQL Server och SharePoint.  Den kan också användas av System Center Data Protection Manager (DPM) för att replikera skyddade data till Azure för redundans och långsiktig lagring.

Skyddade data i Azure Backup lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region. Data replikeras inom samma region och kan också, beroende på typen av valv, replikeras till en annan region för att ytterligare öka återhämtningsförmågan.

Azure Backup har tre grundläggande scenarier.

- **Windows-dator med Azure Backup-agent.** Säkerhetskopiera filer och mappar från valfri Windows-server eller Windows-klient direkt till ditt Azure-säkerhetskopieringsvalv.<br><br>![Windows-dator med Azure Backup-agent](media/operations-management-suite-overview/overview-backup-01.png)
- **System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server.** Använd DPM eller Microsoft Azure Backup Server för att säkerhetskopiera filer och mappar samt programarbetsbelastningar som SQL och SharePoint till lokal lagring och sedan replikera till ditt Azure-säkerhetskopieringsvalv. Har stöd för virtuella Windows- och Linux-datorer på Hyper-V eller VMware.<br><br>![System Center Data Protection Manager (DPM) eller Microsoft Azure Backup Server](media/operations-management-suite-overview/overview-backup-02.png)
- **Azure Virtual Machine-tillägg.** Säkerhetskopiera virtuella Windows- eller Linux-datorer i Azure till ditt Azure Backup-valv.<br><br>![Azure Virtual Machine-tillägg](media/operations-management-suite-overview/overview-backup-03.png)



#### <a name="azure-site-recovery"></a>Azure Site Recovery
[Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery) ger verksamhetskontinuitet genom att dirigera replikering av lokala virtuella och fysiska datorer till Azure, eller till en sekundär plats. Om din primära plats inte är tillgänglig görs en växling till den sekundära platsen så att användarna kan fortsätta att arbeta och sedan görs en återställning när systemen fungerar som de ska igen. 

Azure Site Recovery ger hög tillgänglighet för servrar och program.  Tjänsten bidrar till din BCDR-strategi för affärskontinuitet och haveriberedskap genom att samordna replikering, redundansväxling och återställning av lokal virtuella Hyper-V-datorer, virtuella VMware-datorer och fysiska Windows- eller Linux-servrar. Du kan replikera datorer till ett sekundärt datacenter eller utöka ditt datacenter genom att replikera dem till Azure. Site Recovery tillhandahåller också enkel redundansväxling och återställning för arbetsbelastningar. Funktionen kan integreras med mekanismer för haveriberedskap som SQL Server AlwaysOn och tillhandahåller återställningsplaner för enkel redundansväxling av arbetsbelastningar som är nivåindelade över flera datorer.

Azure Site Recovery har tre grundläggande replikeringsscenarier.

- **Replikering av virtuella Hyper-V-datorer.**  Om Hyper-V-datorer hanteras i VMM-moln kan du replikera till ett sekundärt datacenter eller till Azure Storage. Replikeringen till Azure sker via en säker Internetanslutning. Replikeringen till ett sekundärt datacenter sker via det lokala nätverket.  Om virtuella Hyper-V-datorer inte hanteras av VMM kan du bara replikera till Azure Storage. Replikeringen till Azure sker via en säker Internetanslutning.<br><br>![Replikering av virtuella Hyper-V-datorer](media/operations-management-suite-overview/overview-siterecovery-hyperv.png)
- **Replikering av virtuella VMware-datorer.**  Du kan replikera virtuella VMware-datorer till ett sekundärt datacenter som kör VMware eller till Azure Storage. Replikeringen till Azure kan ske via en VPN för plats till plats eller Azure ExpressRoute eller via en säker Internetanslutning. Replikeringen till ett sekundärt datacenter sker via InMage Scout-datakanalen.<br><br>![Replikering av virtuella VMware-datorer](media/operations-management-suite-overview/overview-siterecovery-vmware.png)
- **Replikering av fysiska Windows- och Linux-servrar.**  Du kan replikera fysiska servrar till ett sekundärt datacenter eller till Azure Storage. Replikeringen till Azure kan ske via en VPN för plats till plats eller Azure ExpressRoute eller via en säker Internetanslutning. Replikeringen till ett sekundärt datacenter sker via InMage Scout-datakanalen. Azure Site Recovery har en OMS-lösning som visar en del statistik, men du måste använda Azure Portal för att utföra åtgärder.<br><br>![Replikering av fysiska Windows- och Linux-servrar](media/operations-management-suite-overview/overview-siterecovery-physical.png)


Site Recovery lagrar metadata i valv som finns i en viss geografisk Azure-region. Inga replikerade data lagras av Site Recovery-tjänsten.

## <a name="management-solutions"></a>Hanteringslösningar
[Hanteringslösningar](operations-management-suite-solutions.md) är färdiga logikuppsättningar för olika scenarier som utnyttjar en eller flera OMS-tjänster.  Olika lösningar är tillgängliga från Microsoft och partners. Du kan enkelt lägga till dem i din Azure-prenumeration för att öka värdet av din OMS-investering.  Som partner kan du skapa egna lösningar för dina program och tjänster och tillhandahålla dem till användare via Azure Marketplace eller Azure-snabbstartsmallar.

Ett bra exempel på en lösning som utnyttjar flera tjänster för att utöka funktionaliteten är [lösningen Hantering av uppdateringar](oms-solution-update-management.md).  Den lösningen använder Log Analytics-agenten för Windows och Linux för att samla in information om nödvändiga uppdateringar på varje agent.  Dessa data skrivs till Log Analytics-databasen där du kan analysera dem med en instrumentpanel.  När du skapar en distribution används runbook-flöden i Azure Automation för att installera nödvändiga uppdateringar.  Du hanterar hela processen i portalen och behöver inte tänka på de bakomliggande detaljerna.

![Lösning](media/operations-management-suite-overview/overview-solution.png)

Med de flesta av lösningarna kan du utföra en eller flera av följande funktioner.

- Samla in ytterligare information.  Log Analytics samlar in en mängd olika data från klienter och tjänster, inklusive händelse- och prestandadata.  En hanteringslösning kan samla in ytterligare information som inte är tillgänglig från andra datakällor, ofta med hjälp av runbook-flöden för Azure Automation.
- Tillhandahålla ytterligare analys av insamlad information.  I hanteringslösningarna ingår instrumentpaneler och vyer för att analysera och visualisera data.  Dessa länkar tillbaka till fördefinierade loggsökningar, vilket gör att du kan söka vidare i mer detaljerade data.  De kan också analysera data som redan har samlats in i databasen, till exempel söka efter mönster som tyder på hot baserat på säkerhetshändelser.
- Lägga till funktioner.  För att kunna tillhandahålla ytterligare funktioner kan vissa lösningar från Microsoft bygga vidare på funktionerna i huvudtjänsterna.  Tjänstkartan tillhandahåller till exempel en egen konsol att utforska och mappar server- och processberoenden i realtid.
Microsoft och partner lägger med jämna mellanrum till nya lösningar i OMS så att du hela tiden kan öka värdet på din investering.  Du kan bläddra bland och installera Microsoft-lösningar via katalogen med lösningar i OMS-portalen eller bläddra bland och installera lösningar från Microsoft och partner via Azure Marketplace i Azure Portal.  

![Lösningsgalleriet](media/operations-management-suite-overview/solution-gallery.png)


## <a name="next-steps"></a>Nästa steg
* Läs mer om [Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics).
* Läs mer om [Azure Automation](../automation/automation-intro.md).
* Läs mer om [Azure Backup](http://azure.microsoft.com/documentation/services/backup).
* Läs mer om [Azure Site Recovery](http://azure.microsoft.com/documentation/services/site-recovery).
* Utforska [lösningarna som är tillgängliga](../log-analytics/log-analytics-add-solutions.md) i olika OMS-erbjudanden. 


