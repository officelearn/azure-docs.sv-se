---
title: Azure operativ säkerhet | Microsoft Docs
description: Lär dig mer om Microsoft Operations Management Suite (OMS), dess tjänster och hur det fungerar.
services: security
documentationcenter: na
author: UnifyCloud
manager: mbaldwin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: d5bea758a0b2a950f51ec443707d9d32dee9cbc4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33895688"
---
# <a name="azure-operational-security"></a>Azure operativ säkerhet
## <a name="introduction"></a>Introduktion

### <a name="overview"></a>Översikt
Vi vet att säkerhet är ett jobb i molnet och det är viktigt att du hitta korrekt och rimlig information om säkerheten i Azure. Ett av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av en mängd säkerhetsverktyg och funktioner som är tillgängliga. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar för säkra Azure-plattformen. Windows Azure måste tillhandahålla sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent accountability.

Som hjälper kunder att bättre förstå matris med säkerhetskontroller implementerat i Microsoft Azure från båda kundens och Microsoft operativa perspektiv, faktabladet ”Azure operativ säkerhet”, skrivs som ger en omfattande Titta på operativ säkerhet som är tillgängliga med Windows Azure.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter. Det kan köras Linux behållare med Docker-integration. skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; build-servrar för iOS, Android och Windows enheter. Molntjänsten Azure stöder att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på, eller migrera IT tillgångar, en offentlig molntjänstleverantör du förlita dig på den organisationens förmåga att skydda dina program och data med tjänster och kontrollerna som ger för att hantera säkerheten för din molnbaserade tillgångar.

Azures infrastruktur från funktionen är utformad för att program som värd för miljontals kunder samtidigt och ger en säker grund som företag kan uppfylla sina säkerhetskrav. Dessutom ger Azure dig en mängd olika konfigurerbara säkerhetsalternativ och möjlighet att styra dem så att du kan anpassa säkerhet för att uppfylla de specifika behoven för din organisation distributioner. Det här dokumentet kommer hjälper dig att förstå hur Azure-säkerhet funktioner kan hjälpa dig att uppfylla kraven.

### <a name="abstract"></a>Abstrakt
Azure operativ säkerhet refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure operativ säkerhet bygger på ett ramverk som innehåller kunskap via olika funktioner som är unika för Microsoft, inklusive den Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program och djup medvetenhet om hotbild cybersecurity.

I det här dokumentet beskriver Microsofts strategi för Azure operativ säkerhet i Microsoft Azure cloud platform och omfattar följande tjänster:
1.  [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure nätverksbevakaren](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite

Microsoft Operations Management Suite (OMS) är IT-hanteringslösning för hybridmoln. Används fristående eller om du vill utöka din befintliga System Center-distribution OMS ger maximal flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Microsoft Operations Management Suite](./media/azure-operational-security/azure-operational-security-fig1.png)

Du kan hantera valfri instans i något moln, inklusive lokalt, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrenskraftiga lösningar med OMS. Byggt för molnet första världen erbjuder OMS en ny metod för att hantera ditt företag som är det snabbaste och mest kostnadseffektiva sättet att uppfylla nya utmaningar för företag och hantera nya arbetsbelastningar, program och miljöer i molnet.

### <a name="oms-services"></a>OMS-tjänster

Huvudfunktionerna i OMS tillhandahålls genom en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

| Tjänst  | Beskrivning|
| :------------- | :-------------|
| Log Analytics | Övervaka och analysera tillgänglighet och prestanda för olika resurser inklusive fysiska och virtuella datorer. |
|Automation | Automatisera manuella processer och tillämpa konfigurationer för fysiska och virtuella datorer. |
| Backup | Säkerhetskopiera och återställa kritiska data. |
| Site Recovery | Ge hög tillgänglighet för viktiga program. |

### <a name="log-analytics"></a>Log Analytics

[Log Analytics](http://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning för OMS genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.


Den här metoden kan du konsolidera data från olika källor, så kan du kombinera data från Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.


![Log Analytics](./media/azure-operational-security/azure-operational-security-fig2.png)

Logganalys-tjänsten hanterar molnbaserade data på ett säkert sätt med hjälp av följande metoder:
-   Dataavgränsning
-   datalagring
-   Fysisk säkerhet
-   Hantering av incidenter
-   Kompatibilitet
-   standarder Säkerhetscertifieringar

### <a name="azure-backup"></a>Azure Backup

[Azure-säkerhetskopiering](http://azure.microsoft.com/documentation/services/backup) innehåller data att säkerhetskopiera och återställa tjänster och OMS uppsättning produkter och tjänster.
Tjänsten skyddar dina programdata och sparar dem i åratal utan stora investeringar och med minimala driftkostnader. Det kan säkerhetskopiera data från fysiska och virtuella servrar förutom arbetsbelastningar för program som SQL Server och SharePoint. Det kan även användas av [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) att replikera skyddade data till Azure för redundans och långsiktig lagring.


Skyddade data i Azure Backup lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region. Data replikeras inom samma region och, beroende på vilken typ av valvet, kanske också replikeras till en annan region för ytterligare återhämtning.

### <a name="management-solutions"></a>Hanteringslösningar
[Microsoft Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) är Microsofts molnbaserade IT lösning som hjälper dig att hantera och skydda dina lokala och molnet infrastruktur.


[Hanteringslösningar](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) färdigförpackade består av logics som implementerar en viss management-scenario med en eller flera OMS-tjänster. Olika lösningar är tillgängliga från Microsoft och partners. Du kan enkelt lägga till dem i din Azure-prenumeration för att öka värdet av din OMS-investering. Som partner kan skapa du egna lösningar för att stödja dina program och tjänster och ge dem till användare via Azure Marketplace eller snabb Start mallar.


![Hanteringslösningar](./media/azure-operational-security/azure-operational-security-fig4.png)

Ett bra exempel på en lösning som använder flera tjänster för att ge ytterligare funktionalitet är den [uppdatering hanteringslösning](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Den här lösningen använder den [logganalys](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agent för Windows och Linux att samla in information om nödvändiga uppdateringar på varje agent. Dessa data skrivs till Log Analytics-databasen där du kan analysera dem med en instrumentpanel.

När du skapar en distribution av runbooks i [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) används för att installera nödvändiga uppdateringar. Du hanterar hela processen i portalen och du behöver inte tänka på de bakomliggande detaljerna.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center skyddar resurserna i Azure. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer. Inom tjänsten, du kan definiera principer inte bara mot dina Azure-prenumerationer, utan även mot [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), så du kan vara mer detaljerade.

### <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer

En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser i en viss prenumeration eller resursgrupp.

I Security Center skapar du principer baserat på företagets säkerhetsbehov, hur informationen ska användas och hur känslig den är.

![Säkerhetsprinciper och säkerhetsrekommendationer](./media/azure-operational-security/azure-operational-security-fig5.png)


Principer som ställs in på prenumerationsnivå automatiskt sprids till alla resursgrupper i prenumerationen så som visas i diagrammet på höger sida:


### <a name="data-collection"></a>Datainsamling

Security Center samlar in data från dina virtuella datorer (VM) för att utvärdera deras säkerhetstillstånd, ange säkerhetsrekommendationer och varna för hot. När din första åtkomst Security Center, insamling av data är aktiverat på alla virtuella datorer i din prenumeration. Insamling av data rekommenderas, men du kan avanmäla dig genom att stänga av insamling av data i Security Center-principen.

### <a name="data-sources"></a>Datakällor

- Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

-   Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.

- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.

-   Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.

-   Virtual Machines: Använder information om konfigurationer och säkerhetshändelser, till exempel händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer.

### <a name="data-protection"></a>Dataskydd

Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

-   **Datauppdelning**: Data lagras logiskt separerade på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

-   **Dataåtkomst**: Om du vill ange säkerhetsrekommendationer och undersöka potentiella säkerhetshot, Microsoft-Personal kan komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive kraschdumpfiler, bearbeta-skapande händelser, virtuell disk ögonblicksbilder och artefakter som kan oavsiktligt innehålla kundinformation eller personliga data från dina virtuella datorer. Vi följer den [Microsoft Online Services-licensvillkoren och sekretesspolicyn](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), vilka tillstånd som inte är Microsoft använder kundinformation eller härledd information från den i reklam eller liknande kommersiellt syfte.

-   **Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

### <a name="data-location"></a>Dataplats

Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar. Artefakter lagras centralt i samma region som den virtuella datorn.

-   **Storage-konton**: ett lagringskonto har angetts för varje region där virtuella datorer körs. Det gör att du kan lagra data i samma region som den virtuella dator som data samlas in från.

-   **Azure Security Center Storage**: Information om säkerhetsvarningar, inklusive partneraviseringar, rekommendationer och status för säkerhetstillstånd lagras centralt (för närvarande i USA). Den här informationen kan omfatta relaterad konfigurationsinformation och säkerhetshändelser som samlas in från dina virtuella datorer för att ge dig säkerhetsvarningar, rekommendationer eller säkerhetsrelaterad statusinformation.


## <a name="azure-monitor"></a>Azure Monitor

Den [OMS säkerhet](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) och granska lösningen möjliggör för IT att övervaka alla resurser som kan hjälpa dig att minimera effekten av säkerhetsincidenter aktivt. OMS säkerhet och granska har säkerhetsdomäner som kan användas för att övervaka resurser. Säkerhetsdomän ger snabb åtkomst till alternativ, för säkerhetsövervakning följande domäner beskrivs mer detaljerat:

-   kontroll av skadlig kod
-   Kontroll av uppdateringar
-   Identitets- och.

Azure-Monitor innehåller tips och information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, varningar, automatisk skalning och automation på data från Azure-infrastrukturen (aktivitetsloggen) och varje enskild Azure resurs (diagnostikloggar).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Molnprogram är komplicerade med många rörliga delar. Övervakning tillhandahåller data för att säkerställa att programmet in och körs i ett felfritt tillstånd. Det hjälper dig också att stave ut potentiella problem eller felsöka tidigare viktiga.

Du kan dessutom använda övervakningsdata och få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda eller underhålla eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen


Det är en logg som ger inblick i åtgärder som utfördes på resurser i din prenumeration. Aktivitetsloggen tidigare kallades ”granskningsloggar” eller ”operativa loggar” eftersom den rapporterar kontroll-plan händelser för dina prenumerationer.

![Azure-aktivitetsloggen](./media/azure-operational-security/azure-operational-security-fig7.png)

Använder aktivitetsloggen, kan du bestämma den ' vad som, och när ' för alla skrivåtgärder (PUT, POST, ta bort) tas för de resurser i din prenumeration. Du kan också få status för åtgärden och andra relevanta egenskaper. Aktivitetsloggen omfattar inte läsåtgärder (GET) eller åtgärder för resurser som använder den klassiska modellen.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Dessa loggar orsakat av en resurs och ger omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen.

Till exempel Windows-händelsesystemloggar är en kategori av diagnostiska loggar för virtuella datorer och blob, tabell och kön loggar är kategorier av diagnostiska loggar för lagringskonton.

Diagnostik loggar skiljer sig från den [aktivitetsloggen (kallades granskningsloggen eller arbetsloggen)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger inblick i åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

### <a name="metrics"></a>Mått

Azure-Monitor kan du använda telemetri för att få insyn i prestanda och hälsotillståndet för dina arbetsbelastningar i Azure. Viktigaste är Azure telemetridata mätvärdena (kallas även prestandaräknare) sänds av mest Azure-resurser. Azure-Monitor finns flera sätt att konfigurera och använda dessa [mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) för övervakning och felsökning. Mått är en del av telemetri och gör att du kan göra följande:

-   **Spåra prestanda** för din resurs (till exempel en VM, webbplats eller logik app) genom att rita upp dess mått i en portal diagram och fästa diagrammet på en instrumentpanel.

-   **Få ett meddelande om ett problem** som påverkar prestandan för din resurs när en måttet överskrider ett visst tröskelvärde.

-   **Konfigurera automatiska åtgärder**, till exempel automatisk skalning av en resurs eller startar en runbook när en måttet överskrider ett visst tröskelvärde.

-   **Utföra avancerade analyser** eller rapportering på prestanda eller användning trender för din resurs.

-   **Arkivera** prestanda eller hälsa historiken för din resurs för kompatibilitet eller granskningsändamål.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Det är en funktion i Azure som möjliggör insamling av diagnostikdata på ett distribuerat program. Du kan använda tillägget diagnostik från olika olika källor. Stöds för närvarande är [Azure Cloud Service webb- och arbetsroller](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) med Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Andra Azure-tjänster har sina egna separata diagnostik.

## <a name="azure-network-watcher"></a>Azure Nätverksbevakaren

Det är viktigt för att identifiera säkerhetsproblem i nätverket och att säkerställa kompatibilitet med din IT-säkerhet och regelverk styrning modellen granskning nätverkssäkerheten. Du kan hämta konfigurerade Nätverkssäkerhetsgruppen och säkerhetsregler och effektiva säkerhetsregler med säkerhetsgruppen vy. Du kan fastställa vilka portar som är öppna och utvärdera säkerhetsproblem i nätverket med en lista över regler som används.

[Nätverk Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) är en regionala tjänst som gör att du kan övervaka och diagnostisera villkor på en nivå i till och från Azure. Diagnostiska nätverks- och visualiseringsverktyg som finns tillgängliga med Nätverksbevakaren hjälpa dig att förstå, diagnostisera och få insyn i nätverket i Azure. Den här tjänsten innehåller paketinsamling, nästa hopp, IP-flöde Kontrollera NSG flödet loggar i gruppvyn säkerhet. Scenariot nivån övervakning innehåller en heltäckande vy av nätverksresurser i stället för enskilda resurs nätverksövervakning.

![Azure Nätverksbevakaren](./media/azure-operational-security/azure-operational-security-fig8.png)

Nätverksbevakaren har för närvarande följande funktioner:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Granskningsloggar</a>**-åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa loggar kan visas i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg, till exempel Power BI eller verktyg från tredje part. Granskningsloggar är tillgängliga via portalen, PowerShell, CLI och Rest-API. Mer information om granskningsloggarna finns i Audit åtgärder med Resource Manager. Granskningsloggar är tillgängliga för åtgärder som utförs på alla nätverksresurser.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-flöde verifierar </a>**  -kontrollerar om ett paket tillåts eller nekas baserat på flödet information 5-tuppel paket parametrar (mål-IP, käll-IP, målport, källport och Protocol). Om paketet nekas av en Nätverkssäkerhetsgrupp returneras regeln och Nätverkssäkerhetsgruppen som nekats paketet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Nästa hopp</a>**  -avgör nästa hopp för paket som vidarebefordras i Azure-nätverksinfrastruktur, vilket gör det lättare att diagnostisera eventuella felkonfigurerat användardefinierade vägar.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Säkerhet gruppvyn</a>**  -hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG flöda loggning</a>**  -flöde Nätverkssäkerhetsgrupper loggarna gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel information – käll-IP, mål-IP, källport, målport och protokoll.

## <a name="azure-storage-analytics"></a>Azure Storage-analys

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerade statistik och kapacitet transaktionsdata om begäranden till en storage-tjänst. Transaktioner rapporteras på både API åtgärden nivån och storage-servicenivå och kapacitet rapporteras på tjänstnivå lagring. Mätvärdesdata kan användas för att analysera lagringskvoten på tjänsten, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller data för mått för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Storage Analytics loggning är tillgänglig för den [Blob-, kö-och tabellen](https://docs.microsoft.com/azure/storage/storage-introduction). Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst.

Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en storage-tjänst. Begäranden loggas för bästa prestanda. Loggposter skapas bara om det finns begäranden som görs mot tjänstslutpunkten. För exempel om ett lagringskonto har aktivitet i sin slutpunkt för Blob men inte i tabellen eller kö slutpunkter, endast loggar har som hör till Blob-tjänsten skapats.

Om du vill använda Storage Analytics, måste du aktivera den separat för varje tjänst som du vill övervaka. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/); mer information finns i [övervaka ett lagringskonto i Azure portal](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för tjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkända tabeller (för mått), som kan nås med hjälp av Blob-tjänsten och tabelltjänsten API: er.

Storage Analytics har en 20 TB gräns på mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobbar](https://docs.microsoft.com/azure/storage/storage-analytics) i en behållare med namnet $logs, som skapas automatiskt när Storage Analytics har aktiverats för ett lagringskonto.

Följande åtgärder utförs av Storage Analytics är fakturerbar:

-   Begäranden om att skapa BLOB för loggning
-   Begäranden om att skapa tabellentiteter för mått.

> [!Note]
> Mer information om fakturering och datakvarhållningsprinciper finns [Storage Analytics och fakturering för](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> För optimala prestanda som du vill begränsa antalet hög utnyttjade diskar som är anslutna till den virtuella datorn för att undvika möjlig begränsning. Om alla diskar som hög inte används på samma gång, har storage-konto stöd för ett större antal disk.

> [!Note]
> Läs mer om lagringskontogränser [Azure Storage skalbarhets- och prestandamål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Följande typer av autentiserade och anonyma begäranden loggas.

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden |
| Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden |Timeout-fel för både klient och server |
|   Begäranden till analysdata |    Misslyckade GET-begäranden med felkoden 304 (ändra inte) |
| Förfrågningar som görs av Storage Analytics, till exempel loggen skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) avsnitt. | Alla andra misslyckade anonyma begäranden har inte loggats. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformatet](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD innehåller också en fullständig uppsättning funktioner för hantering av identiteter inklusive multifaktorautentisering, registrering av enheten, självbetjäning lösenordshantering, grupphantering via självbetjäning, hantering av privilegierat konto, rollbaserad åtkomst kontroll, övervakning av programanvändning, omfattande granskning och säkerhetsövervakning och aviseringar.

-   Förbättra säkerheten för program med Azure AD-flerfunktionsautentisering och villkorlig åtkomst.

-   Övervaka programanvändning och skydda företaget från avancerade hot med säkerhet övervakning och rapportering.

Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. [Azure Active Directory granska rapporten](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) hjälper kunder att identifiera Privilegierade åtgärder som uppstått i sina Azure Active Directory. Privilegierade åtgärder omfattar höjning ändringar (till exempel skapa användarrollen eller återställning av lösenord), ändra principkonfigurationer (till exempel lösenordsprinciper) eller ändringar av katalogkonfiguration (till exempel ändringar i federation domäninställningar).

Med rapporterna får granskningsposten för händelsenamnet aktören som utförde åtgärden målresursen påverkas av ändringen, samt datum och tid (UTC). Kunder kan hämta listan över granskningshändelser för sina Azure Active Directory via den [Azure-portalen](https://portal.azure.com/), enligt beskrivningen i [visa granskningsloggarna](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Här är en lista över de rapporter som ingår:

| Säkerhetsrapporter  | Aktivitetsrapporter| Granskningsrapporter |
| :------------- | :-------------| :-------------|
|Inloggningar från okända källor | Programanvändning: sammanfattning | Kataloggranskningsrapport |
|Inloggningar efter flera fel | Programanvändning: detaljerad |   |
|Inloggningar från flera geografiska områden | Instrumentpanel för program |  |
|Inloggningar från IP-adresser med misstänkt aktivitet |Kontoetableringsfel |  |
|Oregelbunden inloggningsaktivitet |Enskilda användarenheter |  |
|Inloggningar från potentiellt infekterade enheter |Aktivitet för enskilda användare |   |
|Användare med avvikande inloggningsaktivitet |Aktivitetsrapport för grupper |   |
| |Aktivitetsrapport över registrering av lösenordsåterställning |   |
| |Lösenordsåterställningsaktivitet |   | |



Data för de här rapporterna kan vara användbara för ditt program, till exempel SIEM-system, granskning och business intelligence-verktyg. Azure AD reporting [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) ger programmatisk åtkomst till data via en uppsättning REST-baserad API: er. Du kan anropa API: erna från olika programmeringsspråk språk och verktyg.

Händelser i Azure AD granska rapporten finns kvar i 180 dagar.

> [!Note]
> Mer information om kvarhållning i rapporter finns [Azure Active Directory Rapportkvarhållningsregler](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

För kunder som vill lagra sina [granskningshändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) för längre kvarhållningsperioder Reporting-API kan användas för att regelbundet hämta granskningshändelser till ett separat datalager.

## <a name="summary"></a>Sammanfattning

Den här artikeln sammanfattningar skydda din integritet och skydda dina data, samtidigt som de levererar programvaror och tjänster som hjälper dig hantera IT-infrastrukturen i din organisation. Microsoft förstår att när de överlåta sina data till andra förtroendet kräver omfattande säkerhet. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Säkra och skydda data är högsta prioritet på Microsoft.

Den här artikeln

-   Hur data som samlas in, bearbetas och skyddas i Operations Management Suite (OMS).

-   Analysera händelser snabbt via flera datakällor. Identifiera säkerhetsrisker och förstå omfattning och påverkan från hot och attacker för att mildra skador från en säkerhetsöverträdelse.

-   Identifiera attackmönster genom att visualisera utgående IP-trafik och typer av skadliga hot. Förstå säkerhetstillståndet av hela miljön oavsett plattform.

-   Samla in alla loggen och händelsen data som krävs för en säkerhets- eller efterlevnad granskning. Snedstreck tid och resurser som krävs för att ange en säkerhetsgranskning med en fullständig sökbara och exportera loggen och händelsedatauppsättning.

<ul>
<li>Samla in säkerhetshändelser, granska och intrång analys att hålla ett öga dina tillgångar:</li>
<ul>
<li>Säkerhet</li>
<li>Viktiga problem</li>
<li>Sammanfattningar hot</li>
</ul>
</ul>

## <a name="next-steps"></a>Nästa steg

- [Design- och operativ säkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft utformar dess tjänster och program med säkerhet i åtanke för att säkerställa att dess infrastruktur i molnet är flexibel och försvaras från attacker.

- [Operations Management Suite | Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance)

Använd Microsoft security data och analys för att utföra intelligent och effektiv hotidentifiering.

- [Planera för Azure Security Center och åtgärder](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) en uppsättning steg och aktiviteter som du kan följa för att optimera din användning av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell.

