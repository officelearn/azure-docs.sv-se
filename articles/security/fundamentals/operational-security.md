---
title: Azure operativ säkerhet | Microsoft-dokument
description: Lär dig mer om Microsoft Azure Monitor-loggar, dess tjänster och hur det fungerar.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 34c0c52945abc6e0ab74b1cb180581c76464bee8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749953"
---
# <a name="azure-operational-security"></a>Azure operativ säkerhet
## <a name="introduction"></a>Introduktion

### <a name="overview"></a>Översikt
Vi vet att säkerhet är jobb ett i molnet och hur viktigt det är att du hittar korrekt och aktuell information om Azure-säkerhet. En av de bästa anledningarna till att använda Azure för dina program och tjänster är att dra nytta av det breda utbudet av säkerhetsverktyg och funktioner som finns tillgängliga. Dessa verktyg och funktioner gör det möjligt att skapa säkra lösningar på den säkra Azure-plattformen. Windows Azure måste tillhandahålla sekretess, integritet och tillgänglighet för kunddata, samtidigt som transparent ansvarsskyldighet aktiveras.

För att hjälpa kunderna att bättre förstå de olika säkerhetskontroller som implementeras i Microsoft Azure ur både kundens och Microsofts operativa perspektiv, skrivs det här faktabladet, "Azure Operational Security", som ger en omfattande titta på driftsäkerheten som är tillgänglig med Windows Azure.

### <a name="azure-platform"></a>Azure-plattform
Azure är en offentlig molntjänstplattform som stöder ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Det kan köra Linux-behållare med Docker integration; skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; skapa back-ends för iOS-, Android- och Windows-enheter. Azure Cloud-tjänsten stöder samma teknik som miljontals utvecklare och IT-proffs redan förlitar sig på och litar på.

När du bygger på eller migrerar IT-tillgångar till en offentlig molntjänstleverantör förlitar du dig på organisationens förmåga att skydda dina program och data med de tjänster och de kontroller de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Azures infrastruktur är utformad från anläggningen till program för att vara värd för miljontals kunder samtidigt, och det ger en tillförlitlig grund på vilken företag kan uppfylla sina säkerhetskrav. Dessutom ger Azure dig ett brett utbud av konfigurerbara säkerhetsalternativ och möjligheten att styra dem så att du kan anpassa säkerheten för att uppfylla de unika kraven i organisationens distributioner. Det här dokumentet hjälper dig att förstå hur Azure-säkerhetsfunktioner kan hjälpa dig att uppfylla dessa krav.

### <a name="abstract"></a>Sammanfattning
Azure Operational Security refererar till tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda deras data, program och andra resurser i Microsoft Azure. Azure Operational Security bygger på ett ramverk som innehåller den kunskap som erhållits genom olika funktioner som är unika för Microsoft, inklusive Microsoft Security Development Lifecycle (SDL), Microsoft Security Response Center-programmet, och djup medvetenhet om cybersäkerhetshotlandskapet.

I det här faktabladet beskrivs Microsofts metod för Azure-driftsäkerhet inom Microsoft Azure-molnplattformen och följande tjänster:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-intro.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure Network watcher](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage-analys](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active-katalog](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor-loggar

Microsoft Azure Monitor-loggar är IT-hanteringslösningen för hybridmolnet. Azure Monitor-loggarna används ensamma eller för att utöka din befintliga System Center-distribution och ger dig maximal flexibilitet och kontroll för molnbaserad hantering av din infrastruktur.

![Azure Monitor-loggar](./media/operational-security/azure-operational-security-fig1.png)

Med Azure Monitor-loggar kan du hantera alla instanser i alla moln, inklusive lokala, Azure, AWS, Windows Server, Linux, VMware och OpenStack, till en lägre kostnad än konkurrerande lösningar. Azure Monitor-loggarna är byggda för molnet först och erbjuder en ny metod för att hantera ditt företag som är det snabbaste och mest kostnadseffektiva sättet att möta nya affärsutmaningar och hantera nya arbetsbelastningar, program och molnmiljöer.

### <a name="azure-monitor-services"></a>Azure Monitor-tjänster

Kärnfunktionerna i Azure Monitor-loggar tillhandahålls av en uppsättning tjänster som körs i Azure. Varje tjänst tillhandahåller en specifik hanteringsfunktion, och du kan kombinera tjänster för olika hanteringsscenarier.

| Tjänst  | Beskrivning|
| :------------- | :-------------|
| Azure Monitor-loggar | Övervaka och analysera tillgänglighet och prestanda för olika resurser inklusive fysiska och virtuella datorer. |
|Automation | Automatisera manuella processer och tillämpa konfigurationer för fysiska och virtuella datorer. |
| Säkerhetskopiering | Säkerhetskopiera och återställa viktiga data. |
| Site Recovery | Ge hög tillgänglighet för viktiga program. |

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics) tillhandahåller övervakningstjänster genom att samla in data från hanterade resurser till en central databas. Dessa data kan omfatta händelser, prestandadata eller anpassade data via API:t. När data har samlats in är de tillgängliga för avisering, analys och export.


Med den här metoden kan du konsolidera data från olika källor, så att du kan kombinera data från dina Azure-tjänster med din befintliga lokala miljö. Metoden innebär också en tydlig separation mellan insamling av data från de åtgärder som vidtas för dessa data, vilket gör att alla åtgärder är tillgängliga för alla typer av data.


![Azure Monitor-loggar](./media/operational-security/azure-operational-security-fig2.png)

Azure Monitor-tjänsten hanterar dina molnbaserade data på ett säkert sätt med hjälp av följande metoder:
-   datasegregering
-   lagring av data
-   fysisk säkerhet
-   hantering av incidenter
-   Överensstämmelse
-   certifieringar av säkerhetsstandarder

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](https://azure.microsoft.com/documentation/services/backup) tillhandahåller datasäkerhetskopiering och återställningstjänster och är en del av Azure Monitor-paketet med produkter och tjänster.
Tjänsten skyddar dina programdata och sparar dem i åratal utan stora investeringar och med minimala driftkostnader. Det kan säkerhetskopiera data från fysiska och virtuella Windows-servrar utöver programarbetsbelastningar som SQL Server och SharePoint. Den kan också användas av [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) för att replikera skyddade data till Azure för redundans och långsiktig lagring.


Skyddade data i Azure Backup lagras i ett säkerhetskopieringsvalv som finns i en viss geografisk region. Data replikeras inom samma region och, beroende på vilken typ av valv, kan också replikeras till en annan region för ytterligare återhämtning.

### <a name="management-solutions"></a>Hanteringslösningar
[Azure Monitor](../../security-center/security-center-intro.md) är Microsofts molnbaserade IT-hanteringslösning som hjälper dig att hantera och skydda din lokala infrastruktur och molninfrastruktur.


[Management Solutions](../../monitoring/monitoring-solutions.md) är färdiga uppsättningar av logik som implementerar ett visst hanteringsscenario med hjälp av en eller flera Azure Monitor-tjänster. Olika lösningar är tillgängliga från Microsoft och från partner som du enkelt kan lägga till i din Azure-prenumeration för att öka värdet på din investering i Azure Monitor. Som partner kan du skapa egna lösningar för att stödja dina program och tjänster och ge dem till användare via Azure Marketplace eller snabbstartsmallar.


![Hanteringslösningar](./media/operational-security/azure-operational-security-fig4.png)

Ett bra exempel på en lösning som använder flera tjänster för att tillhandahålla ytterligare funktioner är lösning för [uppdateringshantering](../../automation/automation-update-management.md). Den här lösningen använder [Azure Monitor-loggagenten](../../log-analytics/log-analytics-queries.md) för Windows och Linux för att samla in information om nödvändiga uppdateringar på varje agent. Den skriver dessa data till Azure Monitor loggar databasen där du kan analysera den med en medföljande instrumentpanel.

När du skapar en distribution används runbooks i [Azure Automation](../../automation/automation-intro.md) för att installera nödvändiga uppdateringar. Du hanterar hela processen i portalen och du behöver inte tänka på de bakomliggande detaljerna.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center hjälper till att skydda dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering över dina Azure-prenumerationer. Inom tjänsten kan du definiera principer inte bara mot dina Azure-prenumerationer, men också mot [resursgrupper](../../azure-resource-manager/management/overview.md#resource-groups), så att du kan vara mer detaljerad.

### <a name="security-policies-and-recommendations"></a>Säkerhetsprinciper och säkerhetsrekommendationer

En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser i en viss prenumeration eller resursgrupp.

I Security Center skapar du principer baserat på företagets säkerhetsbehov, hur informationen ska användas och hur känslig den är.

![Säkerhetsprinciper och säkerhetsrekommendationer](./media/operational-security/azure-operational-security-fig5.png)


Principer som är aktiverade på prenumerationsnivå sprids automatiskt till alla resursgrupper i prenumerationen enligt diagrammet till höger:


### <a name="data-collection"></a>Datainsamling

Security Center samlar in data från dina virtuella datorer (VM) för att utvärdera deras säkerhetstillstånd, ange säkerhetsrekommendationer och varna för hot. När du först öppnar Security Center aktiveras datainsamling på alla virtuella datorer i din prenumeration. Insamling av data rekommenderas, men du kan avanmäla dig genom att stänga av insamling av data i Security Center-principen.

### <a name="data-sources"></a>Datakällor

- Azure Security Center analyserar data från följande källor för att ge dig information om säkerhetstillstånd, identifiera säkerhetsproblem, rekommendera lösningar och upptäcka aktiva hot:

-   Azure-tjänster: Använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resursprovider.

- Nätverkstrafik: Använder samplade metadata för nätverkstrafiken från Microsofts infrastruktur, till exempel källans/målets IP-adress/port, paketstorlek och nätverksprotokoll.

-   Partnerlösningar: Använder säkerhetsaviseringar från integrerade partnerlösningar, till exempel brandväggar och lösningar mot skadlig kod.

-   Virtual Machines: Använder information om konfigurationer och säkerhetshändelser, till exempel händelser och granskningsloggar i Windows, IIS-loggar, syslog-meddelanden och kraschdumpfiler från dina virtuella datorer.

### <a name="data-protection"></a>Dataskydd

Azure Security Center hjälper kunder att förhindra, upptäcka och svara på hot genom att samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurationsinformation, metadata, händelseloggar, kraschdumpfiler och annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

-   **Datauppdelning**: Data lagras logiskt och separat på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

-   **Dataåtkomst:** För att tillhandahålla säkerhetsrekommendationer och undersöka potentiella säkerhetshot kan Microsoft-personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive kraschdumpfiler, processskapande händelser, ögonblicksbilder av VM-diskar och artefakter, som oavsiktligt kan inkludera kunddata eller personuppgifter från dina virtuella datorer. Vi följer [villkoren och sekretesspolicyn](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)för Microsoft Online Services , där det anges att Microsoft inte använder kunddata eller hämtar information från den för reklam eller liknande kommersiella ändamål.

-   **Dataanvändning**: Microsoft använder mönster och hotinformation som identifieras från flera klientorganisationer i syfte att förbättra våra skydds- och identifieringsfunktioner. Vi gör detta i enlighet med våra sekretessåtaganden som beskrivs i [sekretesspolicyn](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Dataplats

Azure Security Center samlar in tillfälliga kopior av dina kraschdumpfiler och analyserar dem efter tecken på kryphål och säkerhetsintrång. Azure Security Center utför den här analysen inom samma geografiska region som arbetsytan och tar bort de tillfälliga kopiorna när analysen är klar. Artefakter lagras centralt i samma region som den virtuella datorn.

-   **Dina lagringskonton:** Ett lagringskonto har angetts för varje region där virtuella datorer körs. Det gör att du kan lagra data i samma region som den virtuella dator som data samlas in från.

-   **Azure Security Center Storage**: Information om säkerhetsvarningar, inklusive partneraviseringar, rekommendationer och status för säkerhetstillstånd lagras centralt (för närvarande i USA). Den här informationen kan omfatta relaterad konfigurationsinformation och säkerhetshändelser som samlas in från dina virtuella datorer för att ge dig säkerhetsvarningar, rekommendationer eller säkerhetsrelaterad statusinformation.


## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor loggar säkerhets-](../../security-center/security-center-monitoring.md) och granskningslösning gör det möjligt för IT-avdelningen att aktivt övervaka alla resurser, vilket kan bidra till att minimera effekten av säkerhetsincidenter. Azure Monitor loggar Säkerhet och granskning har säkerhetsdomäner som kan användas för att övervaka resurser. Säkerhetsdomänen ger snabb åtkomst till alternativ för säkerhetsövervakning av följande domäner beskrivs mer information:

-   Utvärdering av skadlig kod
-   Kontroll av uppdateringar
-   Identitet och åtkomst.

Azure Monitor ger pekare till information om specifika typer av resurser. Den erbjuder visualisering, fråga, routning, aviseringar, automatisk skalning och automatisering på data både från Azure-infrastrukturen (Aktivitetslogg) och varje enskild Azure-resurs (diagnostikloggar).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Molnprogram är komplexa med många rörliga delar. Övervakning innehåller data för att säkerställa att ditt program förblir igång i felfritt tillstånd. Det hjälper dig också att avvärja potentiella problem eller felsöka tidigare.

Dessutom kan du använda övervakningsdata för att få djupa insikter om ditt program. Den kunskapen kan hjälpa dig att förbättra programmets prestanda eller underhåll, eller automatisera åtgärder som annars skulle kräva manuella åtgärder.

### <a name="azure-activity-log"></a>Azure-aktivitetslogg


Det är en logg som ger insikt i de åtgärder som utfördes på resurser i din prenumeration. Aktivitetsloggen kallades tidigare "Granskningsloggar" eller "Operativa loggar", eftersom den rapporterar kontrollplanshändelser för dina prenumerationer.

![Azure-aktivitetslogg](./media/operational-security/azure-operational-security-fig7.png)

Med aktivitetsloggen kan du bestämma vilka, vem och när för alla skrivåtgärder (PUT, POST, DELETE) som tas på resurserna i prenumerationen. Du kan också förstå status för åtgärden och andra relevanta egenskaper. Aktivitetsloggen innehåller inte läsåtgärder (GET) eller åtgärder för resurser som använder modellen Klassisk.

### <a name="azure-diagnostic-logs"></a>Diagnostikloggar i Azure

Dessa loggar avges av en resurs och ger omfattande, frekventa data om resursens funktion. Innehållet i dessa loggar varierar beroende på resurstyp.

Windows-händelsesystemloggar är till exempel en kategori av diagnostiklogg för virtuella datorer och blob-, tabell- och köloggar är kategorier av diagnostikloggar för lagringskonton.

Diagnostikloggar skiljer sig från [aktivitetsloggen (tidigare känd som granskningslogg eller driftlogg).](../../azure-monitor/platform/platform-logs-overview.md) Aktivitetsloggen ger insikt i de åtgärder som utfördes på resurser i din prenumeration. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit på egen hand.

### <a name="metrics"></a>Mått

Med Azure Monitor kan du använda telemetri för att få insyn i prestanda och hälsa för dina arbetsbelastningar på Azure. Den viktigaste typen av Azure telemetridata är måtten (även kallade prestandaräknare) som avges av de flesta Azure-resurser. Azure Monitor innehåller flera sätt att konfigurera och använda dessa [mått](../../monitoring/monitoring-data-collection.md) för övervakning och felsökning. Mått är en värdefull källa till telemetri och gör att du kan utföra följande uppgifter:

-   **Spåra resursens prestanda** (till exempel en virtuell dator, webbplats eller logikapp) genom att rita dess mått i ett portaldiagram och fästa diagrammet på en instrumentpanel.

-   **Få ett meddelande om ett problem** som påverkar resursens prestanda när ett mått överskrider ett visst tröskelvärde.

-   **Konfigurera automatiska åtgärder**, till exempel automatisk skalning av en resurs eller bränning av en runbook när ett mått överskrider ett visst tröskelvärde.

-   **Utför avancerad analys** eller rapportering om prestanda- eller användningstrender för din resurs.

-   **Arkivera** prestanda- eller hälsohistoriken för din resurs för efterlevnads- eller granskningsändamål.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Det är funktionen i Azure som möjliggör insamling av diagnostikdata för ett distribuerat program. Du kan använda diagnostiktillägget från olika källor. Stöds för närvarande [är Azure Cloud Service-webb- och arbetsroller,](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service) [virtuella Azure-datorer](../../virtual-machines/windows/overview.md) som kör Microsoft Windows och [Service Fabric](../../azure-monitor/platform/diagnostics-extension-overview.md). Andra Azure-tjänster har sina egna separata diagnostik.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Granskning av nätverkssäkerheten är avgörande för att identifiera nätverkssårbarheter och säkerställa efterlevnad av din IT-säkerhets- och regelstyrningsmodell. Med säkerhetsgruppsvyn kan du hämta de konfigurerade nätverkssäkerhetsgruppen och säkerhetsreglerna och de gällande säkerhetsreglerna. Med listan över regler som tillämpas kan du bestämma vilka portar som är öppna och bedöma nätverkssäkerhetsproblem.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) är en regional tjänst som gör att du kan övervaka och diagnostisera villkor på nätverksnivå i, till och från Azure. Nätverksdiagnostik och visualiseringsverktyg som är tillgängliga med Network Watcher hjälper dig att förstå, diagnostisera och få insikter i nätverket i Azure. Den här tjänsten innehåller paketfångst, nästa hopp, IP-flödesverifiering, säkerhetsgruppvy, NSG-flödesloggar. Övervakning på scenarionivå ger en end-to-end-vy av nätverksresurser i motsats till individuell övervakning av nätverksresurser.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher har för närvarande följande funktioner:

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview">Granskningsloggar</a>**- Åtgärder som utförs som en del av konfigurationen av nätverk loggas. Dessa loggar kan visas i Azure-portalen eller hämtas med hjälp av Microsoft-verktyg som Power BI eller verktyg från tredje part. Granskningsloggar är tillgängliga via portalen, PowerShell, CLI och Rest API. Mer information om granskningsloggar finns i Granskningsåtgärder med Resurshanteraren. Granskningsloggar är tillgängliga för åtgärder som utförs på alla nätverksresurser.


-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-flöde verifierar</a>** - Kontrollerar om ett paket tillåts eller nekas baserat på flödesinformation 5-tuppelpaketparametrar (mål-IP, käll-IP, målport, källport och protokoll). Om paketet nekas av en nätverkssäkerhetsgrupp returneras regeln och nätverkssäkerhetsgruppen som nekade paketet.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview">Nästa hopp</a>** - Bestämmer nästa hopp för paket som dirigeras i Azure Network Fabric, så att du kan diagnostisera eventuella felkonfigurerade användardefinierade vägar.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview">Säkerhetsgruppsvyn</a>** - Hämtar effektiva och tillämpade säkerhetsregler som tillämpas på en virtuell dator.

-   **<a href="https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG Flow-loggning</a>** - Flödesloggar för nätverkssäkerhetsgrupper gör att du kan samla in loggar som är relaterade till trafik som tillåts eller nekas av säkerhetsreglerna i gruppen. Flödet definieras av en 5-tuppelinformation – Käll-IP, mål-IP, källport, målport och protokoll.

## <a name="azure-storage-analytics"></a>Azure Storage Analytics

[Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) kan lagra mått som innehåller aggregerad transaktionsstatistik och kapacitetsdata om begäranden till en lagringstjänst. Transaktioner rapporteras både på API-åtgärdsnivå och på lagringstjänstnivå, och kapacitet rapporteras på lagringstjänstnivå. Måttdata kan användas för att analysera användning av lagringstjänster, diagnostisera problem med begäranden som görs mot lagringstjänsten och för att förbättra prestanda för program som använder en tjänst.

[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller måttdata för ett lagringskonto. Du kan använda dessa data för att spåra begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto. Lagringsanalysloggning är tillgänglig för [Blob-, kö- och Tabelltjänsterna](../../storage/common/storage-introduction.md). Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst.

Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Förfrågningar loggas på bästa sätt. Loggtransaktioner skapas endast om det finns begäranden som görs mot tjänstens slutpunkt. Om ett lagringskonto till exempel har aktivitet i blob-slutpunkten men inte i dess tabell- eller köslutpunkter skapas bara loggar som hör till Blob-tjänsten.

Om du vill använda Storage Analytics måste du aktivera den individuellt för varje tjänst som du vill övervaka. Du kan aktivera den i [Azure-portalen](https://portal.azure.com/). Mer information finns [i Övervaka ett lagringskonto i Azure-portalen](../../storage/common/storage-monitor-storage-account.md). Du kan också aktivera Storage Analytics programmässigt via REST API eller klientbiblioteket. Använd åtgärden Ange tjänstegenskaper för att aktivera Storage Analytics individuellt för varje tjänst.

Aggregerade data lagras i en välkänd blob (för loggning) och i välkända tabeller (för mått), som kan nås med hjälp av Blob-tjänsten och tabelltjänst-API:er.

Storage Analytics har en gräns på 20 TB för mängden lagrade data som är oberoende av den totala gränsen för ditt lagringskonto. Alla loggar lagras i [blockblobar](../../storage/common/storage-analytics.md) i en behållare med namnet $logs, som skapas automatiskt när Storage Analytics är aktiverat för ett lagringskonto.

Följande åtgärder som utförs av Storage Analytics kan faktureras:

-   Begäranden om att skapa blobbar för loggning
-   Begäranden om att skapa tabellentiteter för mått.

> [!Note]
> Mer information om fakturerings- och datalagringsprinciper finns i [Lagringsanalys och fakturering](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing).
> För optimal prestanda vill du begränsa antalet högt använda diskar som är anslutna till den virtuella datorn för att undvika eventuell begränsning. Om alla diskar inte används mycket samtidigt kan lagringskontot stödja en större nummerdisk.

> [!Note]
> Mer information om begränsningar för lagringskonton finns i [Skalbarhetsmål för standardlagringskonton](../../storage/common/scalability-targets-standard-account.md).


Följande typer av autentiserade och anonyma begäranden loggas.

| Autentiserad  | Anonym|
| :------------- | :-------------|
| Lyckade begäranden | Lyckade begäranden |
|Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel | Begäranden med en SAS (Shared Access Signature), inklusive misslyckade och lyckade begäranden |
| Begäranden med en SAS (Shared Access Signature), inklusive misslyckade och lyckade begäranden |Timeoutfel för både klient och server |
|   Begäranden om analysdata |    Misslyckade GET-begäranden med felkoden 304 (har inte ändrats) |
| Begäranden som görs av Storage Analytics själv, till exempel att skapa logg eller ta bort dem, loggas inte. En fullständig lista över de loggade data dokumenteras i avsnitten [Lagringsanalysloggade operationer och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [lagringsanalysloggformat.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) | Alla andra misslyckade anonyma begäranden loggas inte. En fullständig lista över de loggade data dokumenteras i [Storage Analytics Loggade operationer och statusmeddelanden](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) och [Lagring Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD innehåller också en komplett uppsättning funktioner för identitetshantering, inklusive multifaktorautentisering, enhetsregistrering, lösenordshantering för självbetjäning, grupphantering med självbetjäning, privilegierad kontohantering, rollbaserad åtkomst programanvändningsövervakning, omfattande granskning och säkerhetsövervakning och aviseringar.

-   Förbättra programsäkerheten med Azure AD-multifaktorautentisering och villkorlig åtkomst.

-   Övervaka programanvändning och skydda ditt företag från avancerade hot med säkerhetsrapportering och övervakning.

Azure AD (Active Directory Azure) tillhandahåller säkerhets-, aktivitets- och granskningsrapporter för din katalog. [Azure Active Directory Audit Report](../../active-directory/active-directory-reporting-azure-portal.md) hjälper kunder att identifiera privilegierade åtgärder som inträffade i deras Azure Active Directory. Privilegierade åtgärder omfattar höjdändringar (till exempel rollskapande eller återställning av lösenord), ändring av principkonfigurationer (till exempel lösenordsprinciper) eller ändringar i katalogkonfigurationen (till exempel ändringar av domänfederationsinställningar).

Rapporterna innehåller granskningsposten för händelsenamnet, aktören som utförde åtgärden, målresursen som påverkas av ändringen och datum och tid (i UTC). Kunder kan hämta listan över granskningshändelser för sin Azure Active Directory via [Azure-portalen](https://portal.azure.com/)enligt beskrivningen i [Visa dina granskningsloggar](../../active-directory/reports-monitoring/overview-reports.md). Här är en lista över de rapporter som ingår:

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
| |Lösenordsåterställningsaktivitet |   |



Data i dessa rapporter kan vara användbara för dina program, till exempel SIEM-system, granskning och business intelligence-verktyg. Azure [AD-rapporterings-API:erna](../../active-directory/active-directory-reporting-api-getting-started-azure-portal.md) ger programmatisk åtkomst till data via en uppsättning REST-baserade API:er. Du kan anropa dessa API:er från olika programmeringsspråk och verktyg.

Händelser i Azure AD Audit-rapporten behålls i 180 dagar.

> [!Note]
> Mer information om kvarhållning i rapporter finns i [Azure Active Directory Report Retention Policies](../../active-directory/reports-monitoring/reference-reports-data-retention.md).

För kunder som är intresserade av att lagra sina [granskningshändelser](../../active-directory/active-directory-reporting-activity-audit-logs.md) under längre lagringsperioder kan rapporterings-API:et användas för att regelbundet hämta granskningshändelser till ett separat datalager.

## <a name="summary"></a>Sammanfattning

I den här artikeln sammanfattas skyddet av din integritet och skyddar dina data, samtidigt som du levererar programvara och tjänster som hjälper dig att hantera it-infrastrukturen i din organisation. Microsoft inser att när de anförtror sina data till andra kräver detta förtroende sträng säkerhet. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst. Att skydda och skydda data har högsta prioritet på Microsoft.

I den här artikeln förklaras

-   Hur data samlas in, bearbetas och skyddas i Azure Monitor-paketet.

-   Snabbt analysera händelser över flera datakällor. Identifiera säkerhetsrisker och förstå omfattningen och effekten av hot och attacker för att minska skadan av en säkerhetsöverträdelse.

-   Identifiera attackmönster genom att visualisera utgående skadlig IP-trafik och skadliga hottyper. Förstå säkerhetshållningen i hela din miljö oavsett plattform.

-   Samla in alla logg- och händelsedata som krävs för en säkerhets- eller efterlevnadsgranskning. Snedstreck den tid och de resurser som behövs för att tillhandahålla en säkerhetsgranskning med en fullständig, sökbar och exportbar logg- och händelsedatauppsättning.

<ul>
<li>Samla in säkerhetsrelaterade händelser, granskning och överträdelseanalys för att hålla ett vakande öga på dina tillgångar:</li>
<ul>
<li>Säkerhetsposition</li>
<li>Noterbart problem</li>
<li>Sammanfattar hot</li>
</ul>
</ul>

## <a name="next-steps"></a>Efterföljande moment

- [Design och driftsäkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity)

Microsoft utformar sina tjänster och programvara med säkerhet i åtanke för att säkerställa att dess molninfrastruktur är motståndskraftig och försvaras från attacker.

- [Azure Monitor-loggar | Säkerhet & efterlevnad](https://www.microsoft.com/cloud-platform/security-and-compliance)

Använd Microsofts säkerhetsdata och -analys för att utföra mer intelligent och effektiv hotidentifiering.

- [Planering och åtgärder för Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md) En uppsättning steg och uppgifter som du kan följa för att optimera din användning av Security Center baserat på organisationens säkerhetskrav och molnhanteringsmodell.

