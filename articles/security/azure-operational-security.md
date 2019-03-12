---
title: Azure driftsäkerhet | Microsoft Docs
description: Läs mer om Microsoft Azure Monitor-loggar, dess tjänster och hur det fungerar.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 5a407db97803d3c3be898893b7da6c1cb4aa94bb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537005"
---
# <a name="azure-operational-security"></a>Azure driftsäkerhet
## <a name="introduction"></a>Introduktion

### <a name="overview"></a>Översikt
Vi vet att säkerhet är ett jobb i molnet och hur viktigt det är att du hitta korrekt och snabb information om Azure-säkerhet. En av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av en mängd olika säkerhetsverktyg och funktioner som är tillgängliga. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar för säkra Azure-plattformen. Windows Azure måste ange sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent ansvarstagande.

Att hjälpa kunderna att bättre förstå matris med säkerhetskontroller som implementeras i Microsoft Azure från båda kundens och Microsoft operational perspektiv, detta white paper ”Azure driftsäkerhet”, skrivs som tillhandahåller en omfattande Titta på driftsäkerhet som är tillgängliga med Windows Azure.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Det kan köra Linux-behållare med Docker-integrering skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; Bygg serverdelar för iOS, Android och Windows enheter. Molntjänsten Azure har stöd för samma teknik som miljontals utvecklare och IT-proffs redan använder och litar på.

När du bygger på, eller migrerar IT-tillgångar till en offentlig molntjänstleverantör du lita på den organisationens förmåga att skydda dina program och data med tjänsterna och kontroller får för att hantera säkerheten för dina molnbaserade tillgångar.

Azures infrastruktur är utformad från anläggningen till program som värd för miljoner kunder samtidigt, och det ger en säker grund som företaget kan möta sina säkerhetskrav. Dessutom ger Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem så att du kan anpassa säkerheten för att uppfylla de specifika behoven för din organisations distributioner. Det här dokumentet kommer hjälper dig att förstå hur Azure security funktioner kan hjälpa dig att uppfylla dessa.

### <a name="abstract"></a>Abstrakt
Azure driftsäkerhet refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda sina data, program och andra resurser i Microsoft Azure. Azure driftsäkerhet bygger på ett ramverk som innehåller den kunskap som olika funktioner som är unika för Microsoft, inklusive på Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-program och djup medvetenhet om hotlandskapet.

Detta white paper beskriver Microsofts strategi för Azure driftsäkerhet inom Microsoft Azure-molnplattformen och täcker följande tjänster:
1.  [Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

2.  [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

3.  [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

4.  [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

5.  [Azure Storage analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor-loggar

Microsoft Azure Monitor-loggar är IT-hanteringslösningen för hybridmoln. Används fristående eller för att utöka dina befintliga System Center-distributionen, Azure Monitor-loggarna ger dig stor flexibilitet och kontroll för molnbaserad hantering av infrastrukturen.

![Azure Monitor-loggar](./media/azure-operational-security/azure-operational-security-fig1.png)

Du kan hantera en instans i alla moln, inklusive lokala, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrenskraftiga lösningar med Azure Monitor-loggar. Azure Monitor-loggar skapats för den molnbaserade världen, erbjuder ett nytt sätt att hantera ditt företag är det snabbaste och mest kostnadseffektiva sättet att uppfylla nya utmaningar och hantera nya arbetsbelastningar, program och molnmiljöer.

### <a name="azure-monitor-services"></a>Azure Monitor-tjänster

En uppsättning tjänster som körs i Azure tillhandahåller huvudfunktionerna i Azure Monitor-loggar. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

| Tjänst  | Beskrivning|
| :------------- | :-------------|
| Azure Monitor-loggar | Övervaka och analysera tillgänglighet och prestanda för olika resurser inklusive fysiska och virtuella datorer. |
|Automation | Automatisera manuella processer och tillämpa konfigurationer för fysiska och virtuella datorer. |
| Backup | Säkerhetskopiera och återställa kritiska data. |
| Site Recovery | Ge hög tillgänglighet för viktiga program. |

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakning genom att samla in data från hanterade resurser i en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.


Den här metoden kan du konsolidera data från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.


![Azure Monitor-loggar](./media/azure-operational-security/azure-operational-security-fig2.png)

Azure Monitor-tjänsten hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:
-   dataavgränsning
-   datakvarhållning
-   fysisk säkerhet
-   incidenthantering
-   efterlevnad
-   certifieringar av standarder för säkerhet

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) tillhandahåller data säkerhetskopiera och återställa tjänster och är en del av Azure Monitor uppsättning produkter och tjänster.
Tjänsten skyddar dina programdata och sparar dem i åratal utan stora investeringar och med minimala driftkostnader. Det kan säkerhetskopiera data från fysiska och virtuella Windows-servrar samt programarbetsbelastningar som SQL Server och SharePoint. Det kan även användas av [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) att replikera skyddade data till Azure för redundans och långsiktig lagring.


Skyddade data i Azure Backup lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region. Data replikeras inom samma region och, beroende på typen av valv, kan också replikeras till en annan region för ytterligare öka återhämtningsförmågan.

### <a name="management-solutions"></a>Hanteringslösningar
[Azure Monitor](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda dina lokala och molnbaserade infrastruktur.


[Lösningar för hantering av](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solutions) förpaketerade består av logics som implementerar ett visst hanteringsscenario med hjälp av en eller flera Azure Monitor-tjänster. Olika lösningar är tillgängliga från Microsoft och partner att du enkelt kan lägga till din Azure-prenumeration att öka värdet på din investering i Azure Monitor. Du kan skapa egna lösningar för dina program och tjänster och tillhandahålla dem till användare via Azure Marketplace eller Snabbstartsmallarna som partner.


![Hanteringslösningar](./media/azure-operational-security/azure-operational-security-fig4.png)

Ett bra exempel på en lösning som använder flera tjänster för att tillhandahålla ytterligare funktioner är den [uppdateringshanteringslösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-solution-update-management). Den här lösningen använder den [Azure Monitor loggar](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) agent för Windows och Linux att samla in information om nödvändiga uppdateringar på varje agent. Den skriver dessa data till Azure Monitor-loggar lagringsplatsen där du kan analysera dem med en instrumentpanel som ingår.

När du skapar en distribution av runbooks i [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) används för att installera nödvändiga uppdateringar. Du hanterar hela processen i portalen och du behöver inte tänka på de bakomliggande detaljerna.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper dig att skydda dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer. I tjänsten, du kan definiera principer inte bara mot din Azure-prenumerationer, utan även mot [resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups), så du kan vara mer detaljerade.

### <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer

En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser i en viss prenumeration eller resursgrupp.

I Security Center skapar du principer baserat på företagets säkerhetsbehov, hur informationen ska användas och hur känslig den är.

![Säkerhetsprinciper och säkerhetsrekommendationer](./media/azure-operational-security/azure-operational-security-fig5.png)


Principer som ställs automatiskt in på prenumerationsnivå spridas till alla resursgrupper i prenumerationen så som visas i diagrammet på höger sida:


### <a name="data-collection"></a>Datainsamling

Security Center samlar in data från dina virtuella datorer (VM) för att utvärdera deras säkerhetstillstånd, ange säkerhetsrekommendationer och varna för hot. När din första åtkomst Security Center är datainsamling är aktiverat på alla virtuella datorer i din prenumeration. Insamling av data rekommenderas, men du kan avanmäla dig genom att stänga av insamling av data i Security Center-principen.

### <a name="data-sources"></a>Datakällor

- Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

-   Azure-tjänster: Använder information om konfiguration av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.

- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.

-   Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.

-   Virtual Machines: Använder konfigurationsinformation och information om säkerhetshändelser, till exempel Windows-loggar för händelser och granskningsloggar, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer.

### <a name="data-protection"></a>Dataskydd

Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

-   **Dataavgränsning**: Data lagras logiskt separerade på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

-   **Dataåtkomst**: Om du vill ge säkerhetsrekommendationer och undersöka möjliga säkerhetshot kan Microsofts Personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive kraschdumpfiler, bearbeta händelser som skapas, ögonblicksbilder av Virtuella datordiskar och artefakter, som kan oavsiktligt innehålla kunddata eller personuppgifter från dina virtuella datorer. Vi följer de [sekretesspolicy för Microsoft Online Services-villkoren och](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), vilka tillstånd som Microsoft inte använder kunddata eller härleder information från dem för reklamändamål eller i liknande kommersiellt syfte.

-   **Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras över flera klienter för att förbättra våra skydds- och identifieringsfunktioner funktioner; Vi har gjort i enlighet med sekretessåtaganden som beskrivs i vår [sekretesspolicy](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Dataplats

Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar. Artefakter lagras centralt i samma region som den virtuella datorn.

-   **Dina Lagringskonton**: Ett lagringskonto anges för varje region där virtuella datorer körs. Det gör att du kan lagra data i samma region som den virtuella dator som data samlas in från.

-   **Azure Security Center Storage**: Information om säkerhetsvarningar, inklusive partneraviseringar, rekommendationer och status för säkerhetstillstånd lagras centralt, för närvarande i USA. Den här informationen kan omfatta relaterad konfigurationsinformation och säkerhetshändelser som samlas in från dina virtuella datorer för att ge dig säkerhetsvarningar, rekommendationer eller säkerhetsrelaterad statusinformation.


## <a name="azure-monitor"></a>Azure Monitor

Den [Azure Monitor loggar Security](https://docs.microsoft.com/azure/operations-management-suite/oms-security-monitoring-resources) och granska lösningen möjliggör för IT att övervaka alla resurser som hjälper dig att minimera effekten av säkerhetsincidenter aktivt. Azure Monitor-loggar säkerhet och granskning har säkerhetsdomäner som kan användas för att övervaka resurser. Säkerhetsdomänen ger snabb åtkomst till alternativ, för säkerhetsövervakning följande domäner beskrivs mer detaljerat:

-   Utvärdering av skadlig kod
-   Kontroll av uppdateringar
-   Identitet och åtkomst.

Azure Monitor innehåller tips och information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, aviseringar, Automatisk skala och automatisering på data både från Azure-infrastrukturen (aktivitetsloggen) och varje enskild Azure-resurs (diagnostikloggar).

![Azure Monitor](./media/azure-operational-security/azure-operational-security-fig6.png)


Molnprogram är komplexa med alla rörliga delar. Övervakning ger data så att ditt program fungerar och körs i ett felfritt tillstånd. Det hjälper dig också att stave potentiella problem eller felsöka tidigare sådana.

Du kan dessutom använda övervakningsdata för att få djupa insikter om ditt program. Denna kunskap kan hjälpa dig att förbättra programmets prestanda och underhåll eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

### <a name="azure-activity-log"></a>Azure-aktivitetsloggen


Det är en logg som ger insikt i de åtgärder som utförts på resurser i din prenumeration. Aktivitetsloggen tidigare kallades ”granskningsloggar” eller ”Driftloggar” eftersom den rapporterar kontrollplanet händelser för dina prenumerationer.

![Azure-aktivitetsloggen](./media/azure-operational-security/azure-operational-security-fig7.png)

Med aktivitetsloggen kan du fastställa den ”vad, vem, och när” för alla skrivåtgärder (PUT, POST, ta bort) vidtas på resurserna i din prenumeration. Du kan också förstå statusen för åtgärden och andra relevanta egenskaper. Aktivitetsloggen inkluderar inte läsåtgärder (GET) eller åtgärder för resurser som använder den klassiska modellen.

### <a name="azure-diagnostic-logs"></a>Azure diagnostikloggar

Dessa loggar som genereras av en resurs och tillhandahåller omfattande, frekventa data om användningen av den här resursen. Innehållet i de här loggarna varierar efter resurstyp.

Till exempel Windows-händelsesystemloggar är en kategori på diagnostiklogg för virtuella datorer och blob, tabell och köloggar finns kategorier för diagnostikloggar för storage-konton.

Diagnostikloggar skiljer sig från den [aktivitetsloggen (tidigare kallat granskningsloggen eller arbetsloggen)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). Aktivitetsloggen ger insikt i de åtgärder som utförts på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

### <a name="metrics"></a>Mått

Azure Monitor kan du använda telemetri för att få insyn i prestanda och hälsa för arbetsbelastningar på Azure. Viktigaste är Azure telemetridata mått (kallas även prestandaräknare) skickas från de flesta Azure-resurser. Azure Monitor innehåller flera olika sätt att konfigurera och använda dessa [mått](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) för övervakning och felsökning. Mått är en värdefull källa för telemetri och gör det möjligt att utföra följande uppgifter:

-   **Spåra prestanda** för din resurs (till exempel en virtuell dator, webbplats eller logic app) av ritning dess mått i ett portal diagram och fästa diagrammet på en instrumentpanel.

-   **Få ett meddelande om ett problem** som påverkar prestanda för din resurs när ett mått överskrider ett visst tröskelvärde.

-   **Konfigurera automatiska åtgärder**, till exempel automatisk skalning av en resurs eller startas en runbook när ett mått överskrider ett visst tröskelvärde.

-   **Utföra avancerade analyser** eller rapporter på prestanda och användning trender för din resurs.

-   **Arkivera** prestanda eller hälsotillstånd historiken för din resurs för efterlevnad eller granskningsändamål.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Det är kapacitet inom Azure som aktiverar insamlingen av diagnostikdata på ett distribuerat program. Du kan använda diagnostiktillägget från olika olika källor. För närvarande är [Azure Cloud Service Web och Worker-roller](https://docs.microsoft.com/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/overview) som kör Microsoft Windows och [Service Fabric](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics). Andra Azure-tjänster har sina egna separata diagnostik.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Det är viktigt för att identifiera säkerhetsproblem i nätverket och att säkerställa efterlevnad med din IT-säkerhet och föreskrifter styrning modell granskning nätverkssäkerheten. Du kan hämta den konfigurerade Nätverkssäkerhetsgrupp och säkerhetsregler och gällande säkerhetsregler med säkerhetsgruppen vy. Du kan fastställa de portar som är öppna och utvärdera säkerhetsproblem i nätverket med en lista över regler som tillämpas.

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) är en regional tjänst som hjälper dig att övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Nätverksdiagnostik- och visualiseringsverktyg för Network Watcher hjälper dig att förstå, diagnostisera och få information om ditt nätverk i Azure. Den här tjänsten innefattar paketinsamling, nästa hopp, IP-flöde verifiera säkerhetsgruppvy, NSG-flödesloggar. Scenariot på övervakning ger en heltäckande vy av nätverksresurser, till skillnad från enskilda resource nätverksövervakning.

![Azure Network Watcher](./media/azure-operational-security/azure-operational-security-fig8.png)

Network Watcher har för närvarande följande funktioner:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Granskningsloggar</a>**-åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa loggar kan ses i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg, till exempel Power BI eller verktyg från tredje part. Granskningsloggarna är tillgängliga via portalen, PowerShell, CLI och Rest API. Mer information om granskningsloggarna finns i Granska driften med Resource Manager. Granskningsloggarna är tillgängliga för åtgärder på alla nätverksresurser.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-flöde verifierar </a>**  – kontrollerar om ett paket tillåts eller nekas baserat på flow information 5-tuppel paket parametrar (mål-IP, käll-IP, målport, källport och protokoll). Om paketet nekas av en grupp, returneras regeln och Nätverkssäkerhetsgruppen som nekade paketet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Nästa hopp</a>**  -anger nästa hopp för paket som vidarebefordras i Azure-nätverksinfrastruktur, så att du kan diagnostisera eventuella felkonfigurerad användardefinierade vägar.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Säkerhetsgruppvy</a>**  – hämtar de effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG-Flödesloggar loggning</a>**  -flödesloggar för Nätverkssäkerhetsgrupper gör det möjligt att samla in loggar som rör trafik som tillåts eller nekas av säkerhetsregler i gruppen. Flödet definieras av en 5-tuppel-information – käll-IP, mål-IP, källport, målport och protokoll.

## <a name="azure-storage-analytics"></a>Azure Storage-analys

[Lagringsanalys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mätvärden som innehåller aggregerad Transaktionsstatistik och kapacitetsdata om förfrågningar till en lagringstjänst. Transaktioner rapporteras på både den API-åtgärdsnivå och storage-servicenivå och kapacitet rapporteras på tjänstnivå lagring. Måttdata kan användas för att analysera användning av storage-tjänsten, diagnostisera problem med begäranden som görs mot tjänsten storage och förbättra prestanda för program som använder en tjänst.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller mätvärden för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Loggningen i Storage Analytics är tillgängligt för den [Blob-, kö-och Tabelltjänster](https://docs.microsoft.com/azure/storage/storage-introduction). Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst.

Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en lagringstjänst. Förfrågningar loggas på basis av bästa prestanda. Loggposter skapas endast om det finns förfrågningar mot tjänsteslutpunkt. Exempel om ett storage-konto har aktivitet i dess Blob-slutpunkt, men inte i tabell- eller slutpunkter, endast loggar hör till Blob-tjänsten har skapats.

Om du vill använda Storage Analytics, måste du aktivera det individuellt för varje tjänst som du vill övervaka. Du kan aktivera den i den [Azure-portalen](https://portal.azure.com/); mer information finns i [övervaka ett lagringskonto i Azure-portalen](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Du kan använda åtgärden Ange egenskaper för filtjänsten för att aktivera Storage Analytics individuellt för varje tjänst.

Sammanställda data lagras i en välkänd blob (för loggning) och välkänd tabeller (för mått), som kan nås med hjälp av Blob- och Table service API: er.

Lagringsanalys har en 20 TB gräns för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobbar](https://docs.microsoft.com/azure/storage/storage-analytics) i en behållare med namnet $logs, som skapas automatiskt när Storage Analytics är aktiverat för ett lagringskonto.

Följande åtgärder utförs av Storage Analytics är fakturerbara:

-   Begäranden om att skapa blobar för loggning
-   Begäranden om att skapa tabellentiteter för mått.

> [!Note]
> Läs mer om fakturering och datalagringsprinciper [Lagringsanalys och fakturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> För optimala prestanda som du vill begränsa antalet högutnyttjade diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning. Om alla diskar inte är är används mycket på samma gång, har storage-konto stöd för ett större antal disk.

> [!Note]
> Mer information om begränsningar för lagringskonton finns i [skalbarhet för lagring av Azure- och prestandamål](https://docs.microsoft.com/azure/storage/storage-scalability-targets).


Följande typer av autentiserade och anonyma förfrågningar loggas.

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden som använder en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar |
| Begäranden som använder en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar |Timeout-fel för både klient och server |
|   Begäranden till analytics-data |    Misslyckade GET-begäranden med felkoden 304 (har inte ändrats) |
| Begäranden som görs av Storage Analytics, till exempel log skapas eller tas bort, loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformat](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) ämnen. | Alla övriga misslyckade anonyma förfrågningar loggas inte. En fullständig lista över data som loggats dokumenteras i den [Storage Analytics loggade åtgärder och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Storage Analytics loggformat](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |
## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD innehåller också en fullständig uppsättning funktioner för identitethantering inklusive multifaktorautentisering, enhetsregistrering, lösenordshantering via självbetjäning, grupphantering, hantering av Privilegierade, rollbaserad åtkomst åtkomstkontroll, övervakning av programanvändning, omfattande granskning och säkerhetsövervakning och avisering.

-   Förbättra säkerheten med multifaktorautentisering i Azure AD och villkorlig åtkomst.

-   Övervaka programanvändning och skydda ditt företag från avancerade hot med säkerhet rapportering och övervakning.

Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. [Azure-granskningsrapporten för Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) hjälper kunder att identifiera Privilegierade åtgärder som inträffat i Azure Active Directory. Privilegierade åtgärder omfattar höjning ändras (till exempel skapa en roll eller antalet återställningar av lösenord), ändra principkonfigurationer (till exempel lösenordsprinciper) eller ändringar av katalogkonfiguration (till exempel ändras till federationsinställningar för domän).

Rapporterna ger granskningsposten för händelsenamn aktören som utförde åtgärden målresursen påverkas av ändringen, datum och tid (i UTC). Kunderna kan hämta listan över granskningshändelser för deras Azure Active Directory via den [Azure-portalen](https://portal.azure.com/), enligt beskrivningen i [visa granskningsloggarna](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Här är en lista över de rapporter som ingår:

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



Data för de här rapporterna kan användas till att dina program, till exempel SIEM-system, granskning och business intelligence-verktyg. Azure AD-rapportering [API: er](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) ger programmatisk åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från olika programmeringsspråk och verktyg.

Händelser i Azure AD granskningsrapporten behålls i 180 dagar.

> [!Note]
> Mer information om kvarhållning av rapporterna finns i [Azure Active Directory Rapportkvarhållningsregler](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention).

För kunder som vill lagra sina [granskningshändelser](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) för längre kvarhållningsperioder Reporting-API kan användas för att regelbundet hämta granskningshändelser till ett separat datalager.

## <a name="summary"></a>Sammanfattning

Den här artikeln sammanfattningar skydda din integritet och skydda dina data och leverera programvara och tjänster som hjälper dig att hantera IT-infrastrukturen i organisationen. Microsoft kan identifiera att när de överlåter sina data till andra förtroendet kräver omfattande säkerhet. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Säkra och skydda data är högsta prioritet på Microsoft.

Den här artikeln förklarar

-   Hur data som samlas in, bearbetas och skyddas i Azure Monitor-sviten.

-   Analysera händelser snabbt via flera datakällor. Identifiera säkerhetsrisker och förstå omfattning och påverkan från hot och attacker för att mildra skador från en säkerhetsöverträdelse.

-   Identifiera attackmönster genom att visualisera utgående IP-trafik och typer av skadliga hot. Förstå säkerhetspositionen för hela miljön, oavsett plattform.

-   Avbilda alla logg- och händelsedata data som krävs för en säkerhets- eller granskning. Snedstreck tid och resurser som behövs för att ange en säkerhetsgranskning med en fullständig sökbart och exportera logg- och händelsedatauppsättning.

<ul>
<li>Samla in säkerhetsrelaterade händelser, granskning och intrång analys för att hålla ett vaksamt öga dina tillgångar:</li>
<ul>
<li>Säkerhetsposition</li>
<li>Viktigt problem</li>
<li>Sammanfattningar hot</li>
</ul>
</ul>

## <a name="next-steps"></a>Nästa steg

- [Design- och driftsäkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft utformar sin tjänster och programvara med säkerheten i åtanke för att säkerställa att molninfrastrukturen är flexibel och försvaras från attacker.

- [Azure Monitor-loggar | Säkerhet och efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance)

Använd Microsoft säkerhetsdata och analys för att utföra mer intelligenta och effektiva hotidentifiering.

- [Azure Security Center planerings- och](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide) en uppsättning steg och aktiviteter som du kan följa för att optimera användningen av Security Center utifrån din organisations säkerhetskrav och molnhanteringsmodell.

