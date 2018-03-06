---
title: Introduktion till Azure Security | Microsoft Docs
description: "Lär dig mer om Azure-säkerhet, att tjänsterna och hur det fungerar."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 68bba95e177fa8d0261b84f51b0f5285c7fb7417
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2017
---
# <a name="introduction-to-azure-security"></a>Introduktion till Azure-säkerhet
## <a name="overview"></a>Översikt
Vi vet att säkerhet är ett jobb i molnet och det är viktigt att du hitta korrekt och rimlig information om säkerheten i Azure. Ett av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av dess mängd säkerhetsverktyg och funktioner. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar för säkra Azure-plattformen. Microsoft Azure tillhandahåller sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent accountability.

Som hjälper dig att bättre förstå mängden säkerhetsåtgärder som implementerats i Microsoft Azure från både skrivs kundens och Microsoft operations perspektiv, faktabladet ”introduktion till Azure-säkerhet”, för att tillhandahålla en omfattande titt på tillgängliga säkerheten med Microsoft Azure.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser, och enheter. Det kan köras Linux behållare med Docker-integration. skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; build-servrar för iOS, Android och Windows enheter.

Offentliga Azure-molnet och support att samma tekniker miljontals utvecklare och IT-proffs redan förlitar sig på och litar på. När du bygger på, eller migrera IT tillgångar, en offentlig molntjänstleverantör du förlita dig på den organisationens förmåga att skydda dina program och data med tjänster och kontrollerna som ger för att hantera säkerheten för din molnbaserade tillgångar.

Azures infrastruktur från anläggningen har utformats för att program som värd för miljontals kunder samtidigt och ger en säker grund som företag kan uppfylla sina säkerhetskrav.

Dessutom ger Azure dig en mängd olika konfigurerbara säkerhetsalternativ och möjlighet att styra dem så att du kan anpassa säkerhet för att uppfylla de specifika behoven för din organisation distributioner. Det här dokumentet hjälper dig att förstå hur Azure-säkerhet funktioner kan hjälpa dig att uppfylla kraven.

> [!Note]
> Primär fokuserar i det här dokumentet på kund-riktade kontroller som du kan använda för att anpassa och ökar säkerheten för dina program och tjänster.
>
> Vi hittar vissa översiktlig information, men mer detaljerad information om hur Microsoft skyddar Azure-plattformen själva informationen i den [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrakt
Offentligt moln migreringar har inledningsvis styrs av kostnadsbesparingar och flexibilitet att förnya. Säkerhet ansågs stor betydelse för en stund och även en visa proppen för offentliga molnmigrering. Offentligt moln säkerhet har övergått från ett större problem till en drivrutin för molnmigrering. Detta anledningen är stor offentliga molntjänstleverantörer överlägsen förmåga att skydda program och data för molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom Azure ger dig en mängd olika konfigurerbara säkerhetsalternativ och möjlighet att styra dem så att du kan anpassa säkerhet för att uppfylla de specifika behoven för din distribution så att de uppfyller IT-ADMINISTRATÖREN kontrollera principer och följa externa förordningar.

Det här dokumentet beskrivs Microsofts strategi för säkerhet i Microsoft Azure cloud platform:
* Säkerhetsfunktioner som implementeras av Microsoft Azure-infrastrukturen, kunddata och program.
* Azure-tjänster och säkerhetsfunktioner som är tillgängliga för dig att hantera säkerheten för tjänsterna och dina data i Azure-prenumerationer.

## <a name="summary-azure-security-capabilities"></a>Översikt över Azure säkerhetsfunktioner
Följande tabell ger en kort beskrivning av säkerhetsfunktioner som implementeras av Microsoft Azure-infrastrukturen, kunddata och säkra program.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Säkerhetsfunktioner för att skydda Azure-plattformen:
De funktioner som visas i följande är funktioner som du kan granska för att ge garantier att Azure-plattformen hanteras på ett säkert sätt. Länkar har angetts för ytterligare nedåt på hur Microsoft adresser förtroende kundfrågor inom fyra områden: säker plattform, sekretess och kontroller, efterlevnad och insyn.


| [Säker plattform](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Sekretess och kontroller](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Efterlevnad](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Genomskinlighet](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Säkerhet utvecklingscykeln](https://www.microsoft.com/en-us/sdl/)interna granskningar | [Hantera dina data hela tiden](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Hur Microsoft skyddar kunddata i Azure-tjänster](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Obligatoriska säkerhetsutbildning tillbaka grunden kontroller](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc) |  [Kontrollera på plats](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Gemensamt nav för kontroller](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Hur Microsoft hanterar Dataplats i Azure-tjänster](http://azuredatacentermap.azurewebsites.net/)|
| [Testa intrång](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwiwsOCpganRAhWqxVQKHUdiDsMQFghAMAE&url=https%3A%2F%2Fdownloads.cloudsecurityalliance.org%2Fstar%2Fself-assessment%2FStandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx&usg=AFQjCNEYvBky4zNeDQPN6YJGPFRZA7eeZg&sig2=2kkw1lOCP_kzLzgE9RS2Tg&bvm=bv.142059868,d.amc), [intrångsidentifiering, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [granskningar & loggning](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Tillhandahålla dataåtkomst på dina villkor](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Molntjänster på grund av fordringar Checklista](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Som i Microsoft kan komma åt dina data på vilka villkoren](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Läget för bilder datacentret](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fysisk säkerhet [säkra nätverk](https://docs.microsoft.com/azure/security/security-network-overview) | [Svara på polis och rättsväsende](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Efterlevnad av tjänsten, plats och bransch](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Hur Microsoft skyddar kunddata i Azure-tjänster](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Säkerhet Incident svar](http://aka.ms/SecurityResponsepaper), [delade ansvar](http://aka.ms/sharedresponsibility) |[Strikta sekretess standarder](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Granska certifikatutfärdare för Azure-tjänster, genomskinlighet hub](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Säkerhetsfunktioner som Azure för att skydda Data och program
Beroende på tjänstmodell molnet finns variabeln ansvar för som ansvarar för att hantera säkerheten för programmet eller tjänsten. Det finns funktioner i Azure-plattformen som hjälper dig att möta dessa ansvarsområden via inbyggda funktioner och via partnerlösningar som kan distribueras till en Azure-prenumeration.

De inbyggda funktionerna är ordnade i sex (6) huvudområden: åtgärder, program, lagring, nätverk, beräkning och identitet. Ytterligare information om funktioner som är tillgängliga i Azure-plattformen i dessa områden med sex (6) tillhandahålls via sammanfattningsinformation.

## <a name="operations"></a>Åtgärder
Det här avsnittet innehåller ytterligare information om viktiga funktioner i säkerhetsåtgärder och översiktlig information om dessa funktioner.

### <a name="operations-management-suite-security-and-audit-dashboard"></a>Operations Management Suite säkerhet och granska instrumentpanel
Den [OMS säkerhet och granska lösningen](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) tillhandahåller en heltäckande översikt över din organisations IT säkerhetstillståndet med [inbyggda sökfrågor](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) för anmärkningsvärda problem som kräver din uppmärksamhet. Instrumentpanelen för [Säkerhet och granskning](https://technet.microsoft.com/library/mt484091.aspx) är startsidan för allt som rör säkerheten i OMS. Det ger en övergripande inblick i säkerhetstillståndet hos dina datorer. Här kan du också visa alla händelser från de senaste 24 timmarna, sju dagarna eller ett annat anpassat tidsintervall.

Dessutom kan du konfigurera OMS säkerhet och efterlevnad till [automatiskt utföra specifika åtgärder](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) när en viss händelse identifieras.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) kan du arbeta med resurserna i din lösning som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en [Azure Resource Manager-mall](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) för distribution och mallen kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

Azure Resource Manager mallbaserade distributioner att förbättra säkerheten för lösningar som distribueras i Azure eftersom Standardsäkerhet styr inställningar och kan integreras i standardiserad mallbaserade distributioner. Detta minskar risken för fel i programkonfigurationen säkerhet som kan ske vid manuell distribution.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utökningsbar Management-program (APM)-tjänst för webbutvecklare. Du kan övervaka webbprogrammen levande och automatiskt identifiera prestandaavvikelser med Application Insights. Den innehåller kraftfulla analytics verktyg som hjälper dig att diagnostisera problem och för att förstå vad användarna faktiskt göra med dina appar. Det övervakar programmet den tid som den körs, både under testningen och när du har publicerat eller distribuera den.

Application Insights skapar diagram och tabeller som visar dig, till exempel, vilka tider som du får de flesta användare, hur responsiv appen är och hur väl den hanteras av externa tjänster som den är beroende av.

Om det finns krascher, fel eller prestandaproblem kan söka du igenom telemetridata detaljerat för att diagnosticera orsaken. Och tjänsten skickar du e-postmeddelanden om det finns ändringar i tillgänglighet och prestanda för din app. Application Insights blir därmed värdefulla säkerhetsverktyget eftersom det hjälper med tillgängligheten i sekretess, integritet och tillgänglighet säkerhet triad.

### <a name="azure-monitor"></a>Azure Monitor
[Azure-Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) erbjuder visualisering, fråga, routning, varningar, automatisk skalning och automation på data både från Azure-infrastrukturen ([aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) och varje enskild Azure resurs ([diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Du kan använda Azure-Monitor så att du varnas på säkerhetsrelaterade händelser som genereras i Azure-loggarna.

### <a name="log-analytics"></a>Log Analytics
[Logga Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) tillhör [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) – ger en IT-hanteringslösning för både lokalt och från tredje part molnbaserad infrastruktur (till exempel AWS) utöver Azure-resurser. Data från Azure-Monitor kan vidarebefordras direkt till Log Analytics så att du kan se mått och loggfiler för hela miljön på ett ställe.

Logganalys kan vara användbart i kriminaltekniska och andra säkerhetsanalys som verktyget gör det möjligt att snabbt söka igenom stora mängder säkerhetsrelaterade poster med en flexibel frågeställning. Dessutom lokalt [brandväggar och proxyservrar loggar kan exporteras till Azure och tillgängliga för analys med logganalys.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) är en anpassad cloud konsult som hjälper dig att optimera din Azure-distributioner. Den analyserar din resurskonfiguration och användningstelemetri. Rekommenderar lösningar för att förbättra den [prestanda](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [säkerhet](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), och [hög tillgänglighet](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) av dina resurser vid sökning efter möjligheter att [minska din övergripande Azure tillbringar](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor ger säkerhetsrekommendationer, vilket betydande kan förbättra din övergripande säkerhetstillståndet för lösningar som du distribuerar i Azure. De här rekommendationerna hämtas från säkerhetsanalys som utförs av [Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Dessutom hjälper Azure Security Center med säkerhetsåtgärder genom att ge dig en enda instrumentpanel som hämtar aviseringar och rekommendationer som kan åtgärdas omedelbart. Ofta kan du åtgärda problem med en enda klickning i Azure Security Center-konsolen.
## <a name="applications"></a>Program
Avsnittet innehåller ytterligare information om viktiga funktioner i programmet säkerhets- och sammanfattning information om dessa funktioner.

### <a name="web-application-vulnerability-scanning"></a>Web Application säkerhetsproblem genomsökning
Ett av de enklaste sätten att komma igång med att testa säkerhetsrisker på din [Apptjänst-app](https://docs.microsoft.com/azure/app-service/app-service-web-overview) är att använda den [integrering med Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) att utföra en enda klickning säkerhetsproblem genomsökning av din app. Du kan visa testresultaten i en rapport med enkelt att förstå och lär dig att åtgärda varje säkerhetsproblem med stegvisa instruktioner.

### <a name="penetration-testing"></a>Penetrationstester
Om du vill utföra egna test för intrång eller vill använda en annan skanner suite eller providern, måste du följa den [Azure intrång testa godkännandeprocessen](https://security-forms.azure.com/penetration-testing/terms) och få godkännande i förväg för att utföra testerna önskade intrång.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
Brandväggen för webbaserade program (Brandvägg) i [Azure Programgateway](https://azure.microsoft.com/services/application-gateway/) hjälper dig att skydda webbprogram från vanliga webbaserade attacker som SQL injection, webbplatser scripting attacker och sessionskapning. Kommer det förinställda med skydd från hot som identifieras av den [öppna Web Application säkerhet projekt (OWASP) som de översta 10 vanliga säkerhetsproblem](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service
[App Service autentisering / auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-overview) är en funktion som gör det möjligt för programmet att logga in användare så att du inte behöver ändra koden på appens serverdel. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare.

### <a name="layered-security-architecture"></a>Överlappande säkerhetsarkitekturen
Eftersom [Apptjänstmiljöer](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) ger en isolerad körningsmiljö har distribuerats till en [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), utvecklare kan skapa en skiktad säkerhetsarkitekturen att tillhandahålla olika nivåer av nätverksåtkomst för varje program-nivå. En gemensam önskan är att dölja API-servrar från allmän tillgång till Internet och endast låta API: er anropas av överordnad webbprogram. [Nätverkssäkerhetsgrupper (NSG: er)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) kan användas på Azure Virtual Network-undernät som innehåller Apptjänstmiljöer för att begränsa offentlig åtkomst till API-program.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web serverdiagnostik- och programdiagnostik
App Service web apps ange diagnostikfunktion för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i [web serverdiagnostik](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) och [programdiagnostik](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webbservern innehåller två huvudsakliga utvecklingen i diagnostisera och felsöka webbplatser och program.

Den första nya funktionen är realtid statusinformation om programpooler, arbetsprocesser, webbplatser, programdomäner och begäranden som körs. Andra nya fördelarna är detaljerade spårningshändelser som spårar en begäran under hela processen slutförts begäran och svar.

Om du vill aktivera insamling av dessa spårningshändelser kan IIS 7 konfigureras för att automatiskt samla in fullständig spårningsloggar i XML-format för en viss begäran baserat på tiden eller felkoder för svar.

#### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

-   Detaljerad felloggning - information om felet för HTTP-statuskoder som indikerar att en (statuskod 400 eller högre). Detta kan innehålla information som kan hjälpa dig att avgöra varför servern returnerade felkoden.

-   Spårning av misslyckade begäranden - detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och tidsåtgång i varje komponent. Detta kan vara användbart om du försöker öka prestanda för webbplatsen eller isolera vad som orsakar ett specifikt HTTP-fel som ska returneras.

-   Web Server-loggning - Information om HTTP-transaktioner med W3C utökat loggfilsformat. Detta är användbart när du fastställer övergripande plats mätvärden, till exempel antalet förfrågningar som hanteras eller hur många förfrågningar som kommer från en specifik IP-adress.

#### <a name="application-diagnostics"></a>Programdiagnostik
[Programdiagnostik](https://docs.microsoft.com/azure/app-service/web-sites-enable-diagnostic-log) kan du samla in information som produceras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) klassen för att logga information till diagnostik programloggen. I Application Diagnostics finns det två huvudtyper för händelser, de som är relaterade till programprestanda och de som är relaterade till programfel. Fel och problem kan delas upp ytterligare i anslutning, säkerhet och fel. Underavdelningen för fel är ofta relaterad till problem med programkoden.

I Programdiagnostik kan visa du gruppera händelser på följande sätt:

-   Alla (visar alla händelser)
-   Programfel (visar undantagshändelser)
-   Prestanda (visar prestandahändelser)

## <a name="storage"></a>Lagring
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure storage säkerhets- och sammanfattning information om dessa funktioner.

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan skydda ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC). Begränsa åtkomst baserat på de [behöver](https://en.wikipedia.org/wiki/Need_to_know) och [minsta privilegium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Dessa behörigheter beviljas genom att tilldela rollen RBAC grupper och program för ett visst område. Du kan använda [inbyggda RBAC-roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles), till exempel lagring konto deltagare att tilldela behörigheter till användare. Åtkomst till lagring nycklar för en storage-konto med den [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modell kan kontrolleras via rollbaserad åtkomstkontroll (RBAC).

### <a name="shared-access-signature"></a>Signatur för delad åtkomst
En [signatur för delad åtkomst (Shared Access Signature, SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) ger delegerad åtkomst till resurser på ditt lagringskonto. SAS innebär att du ger en klient begränsade behörigheter till objekt i ditt lagringskonto för en angiven period och på en viss uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för ditt konto.

### <a name="encryption-in-transit"></a>Kryptering under överföring
Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Du kan skydda data med hjälp av med Azure Storage:
-   [Kryptering på transportnivå](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Tråd kryptering](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), som [kryptering i SMB 3.0](https://docs.microsoft.com/azure/storage/storage-security-guide) för [Azure-filresurser](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Klientsidans kryptering, att kryptera data innan det överförs till lagringskontot och att dekryptera data när det överförs utanför lagring.

### <a name="encryption-at-rest"></a>Vilande kryptering
I många organisationer är ett obligatoriskt steg mot datasekretess, efterlevnad och data suveränitet i datakryptering i viloläge. Det finns tre Azure storage-säkerhetsfunktioner som tillhandahåller kryptering av data som är ”i vila”:

-   [Lagringstjänstens kryptering](https://docs.microsoft.com/azure/storage/storage-service-encryption) kan du begära att lagringstjänsten automatiskt kryptera data när du skriver till Azure Storage.

-   [Kryptering på klientsidan](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) innehåller också en funktion till kryptering i vila.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator.

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller data för mått för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Storage Analytics loggar detaljerad information om lyckade och misslyckade förfrågningar till en storage-tjänst. Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en storage-tjänst. Begäranden loggas för bästa prestanda. Följande typer av autentiserade begäranden loggas:
-   Lyckade begäranden.

-   Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel.

-   Begäranden som använder en delad signatur åtkomst (SAS), inklusive misslyckade och lyckade begäranden.

-   Begäranden till analysdata.

### <a name="enabling-browser-based-clients-using-cors"></a>Aktivera webbläsarbaserade klienter med hjälp av CORS
[Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) är en mekanism som gör att domäner för att ge behörighet för åtkomst till varandras resurser till varandra. Användaragenten skickar extra rubriker för att säkerställa att JavaScript-kod som lästs in från en viss domän har tillåtelse att komma åt resurser på en annan domän. Den senare domänen svarar den sedan med extra rubriker för att tillåta eller neka den ursprungliga domän har åtkomsten till resurser.

Azure-lagringstjänster stöder nu CORS så att när du har angett CORS-regler för tjänsten en korrekt autentiserad begäran mot tjänsten från en annan domän utvärderas för att avgöra om det är tillåtet enligt de regler som du har angett.
## <a name="networking"></a>Nätverk
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure-nätverk säkerhets- och sammanfattning information om dessa funktioner.

### <a name="network-layer-controls"></a>Nätverkskontroller lager
Åtkomstkontrollen för nätverk är att begränsa anslutning till och från specifika enheter och undernät och representerar kärnan i nätverkssäkerhet. Målet med åtkomstkontrollen för nätverk är att se till att dina virtuella datorer och tjänster är tillgänglig endast användare och enheter som du vill att de tillgängliga.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En [Nätverkssäkerhetsgrupp (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) är en grundläggande tillståndskänslig paket filtrering brandvägg och den kan du kontrollera åtkomst baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er ger inte programmet layer inspektion eller autentiserad åtkomstkontroller. De kan användas för att styra trafik flytta mellan undernät i ett virtuellt Azure-nätverk och trafik mellan ett virtuellt Azure-nätverk och Internet.

#### <a name="route-control-and-forced-tunneling"></a>Dirigera kontroll och Tvingad tunneltrafik
Möjlighet att styra dirigeringsbeteendet på dina virtuella Azure-nätverk är en kritisk nätverket säkerhets- och kontroll funktion. Om du vill kontrollera att all trafik till och från det virtuella Azure-nätverket går genom den virtuella postsäkerhet måste kunna styra och anpassa dirigeringsbeteendet. Du kan göra detta genom att konfigurera användardefinierade vägar i Azure.

[Användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) kan du anpassa inkommande och utgående sökvägar för trafik i och utanför enskilda virtuella datorer eller undernät till att säkerställa den mest säkra väg möjligt. [Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på Internet.

Detta skiljer sig från att kunna acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begäranden från Internet-värdar och så Internet-källkod trafik tillåts inkommande till dessa servrar och webbservrar kan svara.

Tvingad tunneling används vanligtvis för att tvinga utgående trafik till Internet för att gå igenom lokal säkerhet proxyservrar och brandväggar.

#### <a name="virtual-network-security-appliances"></a>Säkerhetsenheter för virtuellt nätverk
Medan Nätverkssäkerhetsgrupper, användardefinierade vägar och Tvingad tunneling ger en hög säkerhetsnivå vid nätverks- och lager i den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), det kan finnas tillfällen när du vill aktivera säkerhet på högre nivåer i stacken. Du kan komma åt dessa förbättrade funktioner för nätverkssäkerhet med hjälp av en Azure network security installation partnerlösning. Du hittar den senaste Azure partnernätverk säkerhetslösningar genom att besöka den [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söka efter ”säkerhet” och ”nätverkssäkerhet”.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Ett virtuellt Azure-nätverk (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure nätverksinfrastruktur som är dedikerad till din prenumeration. Du kan helt styra IP-adressblocken, DNS-inställningarna, säkerhetsprinciperna och routingtabellerna inom det här nätverket. Du kan också segmentera ditt VNet i undernät och placera Azure IaaS-virtuella maskiner (VMs) och/eller [molntjänster (PaaS rollinstanser)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) på virtuella Azure-nätverk.

Du kan dessutom ansluta det virtuella nätverket till ditt lokala nätverk med ett av [anslutningsalternativen](https://docs.microsoft.com/azure/vpn-gateway/) som finns i Azure. I princip kan du expandera ditt nätverk till Azure, med fullständig kontroll över IP-adressblock och de fördelar som Azure på företagsnivå erbjuder.

Azure-nätverk har stöd för olika scenarier för säker fjärråtkomst. Några av dessa är:

-   [Enskilda arbetsstationer kan anslutas till ett virtuellt Azure-nätverk](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Ansluta lokala nätverk till ett virtuellt Azure-nätverk med en VPN-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Ansluta lokala nätverk till ett virtuellt Azure-nätverk med en fast WAN-länk](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Ansluta virtuella Azure-nätverk till varandra](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN-gateway
Om du vill skicka nätverkstrafik mellan dina virtuella Azure-nätverket och den lokala platsen, måste du skapa en VPN-gateway för det virtuella Azure-nätverket. En [VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) är en typ av virtuell nätverksgateway som skickar krypterad trafik mellan en offentlig anslutning. Du kan också använda VPN-gatewayer för att skicka trafik mellan virtuella Azure-nätverk via Azure-nätverk-infrastruktur.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) är en dedikerad WAN-länk där du kan utöka ditt lokala nätverk till Microsoft cloud via en dedikerad privata anslutning underlättas av en provider för anslutningen.

![Express Route](./media/azure-security/azure-security-fig1.png)

Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar överskrider inte det offentliga Internet och därför kan anses vara säkrare än VPN-baserade lösningar. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) ger en [programmet leverans domänkontrollant (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) som en tjänst som erbjuder olika layer 7 belastningsutjämning för ditt program.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

På så sätt kan du optimera web servergruppen produktivitet genom att avlasta CPU beräkningsintensiva SSL-avslutning av Programgatewayen (även kallat ”SSL-avlastning” eller ”SSL-bryggning”). Här finns även andra lager 7 funktioner inklusive resursallokering fördelning av inkommande trafik, cookie-baserad session tillhörighet, URL-sökväg-baserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. Azure Application Gateway är en Layer 7-belastningsutjämnare.

Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt.

Programmet innehåller många program leverans domänkontrollant (ADC) funktioner inklusive HTTP läsa in belastningsutjämning, cookie-baserad session tillhörighet [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) -avlastning, anpassade hälsoavsökningar, stöd för flera platser och många andra.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
Brandvägg för webbaserade program är en funktion i [Azure Programgateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som ger skydd till webbprogram som använder Programgateway för standardfunktioner programmet leverans kontrollen (ADC). Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de 10 vanligaste OWASP-säkerhetsproblemen.

![Brandvägg för webbaserade program](./media/azure-security/azure-security-fig1.png)

-   Skydd mot SQL-inmatning

-   Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

-   Skydd mot åtgärder som inte följer HTTP-protokollet

-   Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

-   Skydd mot robotar, crawlers och skannrar

-   Identifiering av program vanliga felinställningar (det vill säga Apache, IIS osv.)


En centraliserad brandvägg mot webbattacker underlättar säkerhetshanteringen och håller programmet bättre skyddat mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en programgateway med brandvägg för webbprogram.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) kan du styra distributionen av användartrafik för slutpunkter i olika datacenter. Service-slutpunkter som stöds av Traffic Manager innehåller virtuella Azure-datorer, webbprogram och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure. Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en [routning av nätverkstrafik metoden](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) och hälsotillståndet för slutpunkter.

Traffic Manager erbjuder en uppsättning routning av nätverkstrafik metoder efter programbehov för olika, endpoint hälsa [övervakning](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), och automatisk redundans. Traffic Manager är känsligt för fel, inklusive fel på en hel Azure-region.
### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) levererar hög tillgänglighet och nätverksprestanda till dina program. Det är en belastningsutjämnare för lager 4 (TCP, UDP) som distribuerar inkommande trafik mellan felfri instanser av tjänster som definierats i en belastningsutjämnad uppsättning. Azure belastningsutjämnare kan konfigureras för att:

-   Läsa in Utjämna inkommande Internet-trafiken till virtuella datorer. Den här konfigurationen kallas [mot Internet för belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Läs in Utjämna trafiken mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk mellan platser. Den här konfigurationen kallas [intern belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Vidarebefordra externa trafik till en specifik virtuell dator

### <a name="internal-dns"></a>Internt DNS
Du kan hantera listan över DNS-servrar som används i ett VNet i hanteringsportalen eller i konfigurationsfilen på nätverket. Kunden kan lägga till upp till 12 DNS-servrar för varje virtuellt nätverk. När du anger DNS-servrar, är det viktigt att kontrollera att du anger kundens DNS-servrar i rätt ordning för kundens miljö. DNS-servern visar fungerar inte resursallokering. De används i den ordning som de anges. Om den första DNS-servern i listan är går att nå, använder klienten DNS-servern oavsett om DNS-servern fungerar korrekt eller inte. Vill ändra ordningen för DNS-server för kundens virtuella nätverk, ta bort DNS-servrar i listan och lägga till dem i ordningen som kunden. DNS stöder tillgänglighet aspekt av ”CIA” säkerhet triad.

### <a name="azure-dns"></a>Azure DNS
Den [Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), DNS, eller översätter (eller lösa) en webbplats eller tjänst namn till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en tjänst som är värd för DNS-domäner är att tillhandahålla namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. DNS stöder tillgänglighet aspekt av ”CIA” säkerhet triad.
### <a name="log-analytics-nsgs"></a>Log Analytics NSG: er
Du kan aktivera följande diagnostiska loggen kategorier för NSG: er:
-   : Innehåller poster för vilka NSG regler tillämpas på virtuella datorer och instans roller baserat på MAC-adress. Status för de här reglerna samlas var 60: e sekund.

-   Regler för räknaren: innehåller poster för hur många gånger varje NSG-regel används för att neka eller Tillåt trafiken.

### <a name="azure-security-center"></a>Azure Security Center
Security Center hjälper dig att förebygga, upptäcka och åtgärda hot, ger du ökat överblick och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar. Rekommendationer Nätverkscenter runt brandväggar, Nätverkssäkerhetsgrupper, konfigurera regler för inkommande trafik och mycket mer.

Tillgängliga nätverksrekommendationer är följande:

-   [Lägg till nästa generations brandvägg](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) rekommenderar att du lägger till en nästa generations Brandvägg från en Microsoft-partner att öka din säkerhetsskydd

-   [Vidarebefordra trafik via nästa generations Brandvägg endast](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) rekommenderar att du konfigurerar nätverk regler för nätverkssäkerhetsgrupper (NSG) som tvingar inkommande trafik till den virtuella datorn via din nästa generations Brandvägg.

-   [Aktivera Nätverkssäkerhetsgrupper för undernät eller virtuella datorer](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer.

-   [Begränsa åtkomst via Internetuppkopplad slutpunkt](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er.


## <a name="compute"></a>Compute

Avsnittet innehåller ytterligare information om viktiga funktioner i det här området och sammanfattning information om dessa funktioner.

### <a name="antimalware--antivirus"></a>Program mot skadlig kod & Antivirus
Du kan använda program mot skadlig kod från säkerhet leverantörer, till exempel Microsoft, Symantec, Trend Micro, McAfee och Kaspersky med Azure IaaS för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för Azure-molntjänster och virtuella datorer är en funktion av skydd som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på Azure-system. Microsoft Antimalware kan också distribueras med hjälp av Azure Security Center

### <a name="hardware-security-module"></a>Maskinvarusäkerhetsmodul
Kryptering och du inte öka säkerheten själva nycklar som är skyddade. Du kan förenkla hantering och säkerhet för dina kritiska hemligheter och nycklar genom att lagra dem i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault ger möjlighet att lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 Level 2-standarder. Din SQL Server krypteringsnycklarna för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Nyckelvalvet med alla nycklar och hemligheter från ditt program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator
[Azure-säkerhetskopiering](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är en lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan förstöra dina data och mänskliga fel kan introducera buggar i dina program som kan leda till säkerhetsproblem. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

### <a name="azure-site-recovery"></a>Azure Site Recovery
En viktig del av din organisations [business continuity/haveriberedskap (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategi räkna ut hur du kan skydda företagets arbetsbelastningar och appar igång när du planerade och oplanerade avbrott inträffar. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) hjälper till att samordna replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om den primära platsen kraschar.

### <a name="sql-vm-tde"></a>DATAKRYPTERINGSPRINCIP FÖR VM
[Transparent datakryptering (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) och kryptering på kolumnen (radera) är SQL server-funktioner för kryptering. Den här typen av kryptering kräver kunder att hantera och lagra de kryptografiska nycklar som du använder för kryptering.

Tjänsten Azure Key Vault (AKV) är utformade för att förbättra säkerhet och hantering av dessa nycklar i en säker och hög tillgänglighet på. SQL Server-anslutningen kan SQL Server att använda de här nycklarna från Azure Key Vault.

Om du kör SQL Server med lokala datorer, finns steg som du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-datorn. Men för SQL Server på virtuella Azure-datorer, kan du spara tid med hjälp av funktionen Azure Key Vault-integrering. Med några Azure PowerShell-cmdletar för att aktivera den här funktionen, kan du automatisera konfigurationen krävs för en SQL-VM för att komma åt nyckelvalvet.

### <a name="vm-disk-encryption"></a>VM-diskkryptering
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) är en ny funktion som hjälper dig att kryptera din Windows- och Linux IaaS virtuella diskar. Det gäller funktionen industry standard BitLocker i Windows och funktionen DM-Crypt i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i Nyckelvalvet-prenumeration. Lösningen betyder också att krypteras alla data på virtuella diskar i vila i ditt Azure storage.

### <a name="virtual-networking"></a>Virtuella nätverk
Virtuella datorer måste nätverksanslutningen. För att uppfylla detta krav, kräver Azure virtuella datorer måste vara ansluten till ett Azure Virtual Network. Ett virtuellt Azure-nätverk är en logisk konstruktion som bygger på fysisk Azure nätverksinfrastruktur. Varje logiska [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) isoleras från alla andra virtuella Azure-nätverk. Denna isolering kan försäkra nätverkstrafik i din distribution inte är tillgänglig för andra Microsoft Azure-kunder.

### <a name="patch-updates"></a>Patch-uppdateringar
Korrigering uppdateringar utgångspunkt för att hitta och åtgärda eventuella problem och förenkla hanteringen för software update både genom att minska antalet programuppdateringar måste du distribuera i ditt företag och genom att öka dina möjligheter att övervaka kompatibilitet.

### <a name="security-policy-management-and-reporting"></a>Hantering av säkerhetsprinciper och rapportering
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och svara på hot och ger du ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

### <a name="azure-security-center"></a>Azure Security Center
Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## <a name="identify-and-access-management"></a>Identifiera och åtkomsthantering

Skydda system, program och data börjar med identitetsbaserade åtkomstkontroller. Identitets- och hanteringsfunktioner som är inbyggda i Microsoft-produkter och tjänster att skydda din organisations- och personlig information från obehörig åtkomst när du gör den tillgänglig för behöriga användare när och var de behöver den.

### <a name="secure-identity"></a>Skydda identitet
Microsoft använder flera säkerhetspraxis och tekniker över sina produkter och tjänster för att hantera identitet och åtkomst.
-   [Multifaktorautentisering](https://azure.microsoft.com/services/multi-factor-authentication/) kräver att användare använder flera metoder för åtkomst, lokalt och i molnet. Det ger stark autentisering med ett antal alternativ för enkel verifiering när källändringshastigheten användare med en process för enkel inloggning.

-   [Microsoft Authenticator](https://aka.ms/authenticator) tillhandahåller en användarvänlig Multifaktorautentisering som fungerar med både Microsoft Azure Active Directory och Microsoft-konton och har stöd för wearables och fingeravtryck-baserade godkännanden.

-   [Tvingande principer för lösenord](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) ökar säkerheten för traditionella lösenord genom att införa längd och komplexitet krav, tvingas periodiska rotation och kontoutelåsning efter misslyckade autentiseringsförsök.

-   [Token-baserad autentisering](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) möjliggör autentisering via Active Directory Federation Services (AD FS) eller säkra token från tredje part-system.

-   [Rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) kan du bevilja åtkomst baserat på användarens tilldelade roll, vilket gör det enkelt att ge användarna bara mängden åtkomst som de behöver för att utföra sina arbetsuppgifter. Du kan anpassa RBAC per organisationens affärsmodell och risktolerans.

-   [Integrerad Identitetshantering (hybrididentitet)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) gör att du kan behålla kontrollen över användarnas åtkomst på interna datacenter och moln-plattformar, skapar en enda användaridentitet för autentisering och auktorisering för alla resurser.

### <a name="secure-apps-and-data"></a>Skydda data och appar
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), en omfattande identitets- och molnlösning, kan du skydda åtkomsten till data i program på platsen och i molnet och förenklar hanteringen av användare och grupper. Det kombinerar core katalogtjänster, avancerad identitet styrning, säkerhet och hantering av åtkomst, och gör det lätt för utvecklare att bygga principbaserad Identitetshantering i sina appar. Du kan lägga till betalda funktioner med hjälp av Azure Active Directory Basic, Premium P1 och Premium P2-versioner för att förbättra din Azure Active Directory.

| Ledigt / vanliga funktioner     | Grundläggande funktioner    |Premium P1-funktioner |Premium P2-funktioner | Azure Active Directory Join – endast relaterade funktioner för Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Katalogobjekt](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [(Lägg till/Uppdatera/ta bort) för hantering av användare/grupp / användarbaserade etablering, enhetsregistrering](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [enkel inloggning (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [självbetjäning Ändra lösenordet för dina molnanvändare](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Connect (Synkroniseringsmotorn som utökar lokala kataloger till Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [säkerhet / rapporter](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Gruppbaserad åtkomsthantering / etablering](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [Självbetjäning för återställning av lösenord för dina molnanvändare](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [företagsanpassning (inloggning sidor/åtkomstpanelen anpassning)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Self-Service Group och app Management/egen-Service programmet tillägg/dynamiska grupper](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [Self-Service lösenord återställning/ändra/Unlock med lokala återskrivning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [Multi-Factor Autentisering (molnet och lokalt (MFA-Server))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + MIM Server](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [ Lösenord för automatisk förnyelse för gruppkonton](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Ansluta en enhet till Azure AD, skrivbordet SSO Microsoft Passport för Azure AD, administratör Bitlocker-återställning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM automatisk registrering, Self-Service Bitlocker-återställning, ytterligare lokala administratörer att Windows 10-enheter via Azure AD Anslut](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) är en premium-funktion i Azure Active Directory som gör att du kan identifiera molnprogram som används av anställda i din organisation.

- [Azure Active Directory-identitetsskydd](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) är en security-tjänst som använder Azure Active Directory avvikelseidentifiering identifieringsfunktionerna för att tillhandahålla en samlad vy riskhändelser och potentiella sårbarheter som kan påverka din organisations identiteter.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) gör det möjligt att ansluta virtuella Azure-datorer till en domän utan att behöva distribuera domänkontrollanter. Användare logga in på dessa virtuella datorer med sina företagsuppgifter för Active Directory och sömlöst kan komma åt resurser.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en hög tillgänglighet, globala identity management-tjänsten för konsumentinriktade program som kan skalas till flera hundra miljoner identiteter och integrera olika mobila och plattformar. Kunderna kan logga in på alla dina appar via anpassningsbara funktioner som använder den befintliga sociala medier konton eller skapa nya fristående autentiseringsuppgifter.

- [Azure Active Directory B2B-samarbete](https://aka.ms/aad-b2b-collaboration) är en säker integrering partnerlösning som har stöd för dina företagsomfattande relationer genom att aktivera partners åtkomst till företagets program och data selektivt med hjälp av sina självhanterad identiteter.

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) kan du utöka molnfunktioner till Windows 10-enheter för central hantering. Det gör det möjligt för användare att ansluta till företagets eller organisationens molnet via Azure Active Directory och förenklar åtkomsten till appar och resurser.

- [Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) ger enkel inloggning och säker fjärråtkomst för webbprogram finns lokalt.

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Microsoft Azure-säkerhet](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure-tjänster och -funktioner som du kan använda för att skydda dina tjänster och data i Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Förhindra, upptäck och svara på hot med ökad insyn i och kontroll över säkerheten hos dina Azure-resurser

- [Övervakning av säkerhetshälsa i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Övervakningsfunktionerna i Azure Security Center att övervaka kompatibilitet med principer.
