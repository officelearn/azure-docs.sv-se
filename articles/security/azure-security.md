---
title: Introduktion till Azure-säkerhet | Microsoft Docs
description: Läs mer om Azure Security, dess tjänster och hur det fungerar.
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
ms.openlocfilehash: a00f3d2ea4e191bb7860b88d39ff0ccaa338e0ff
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714244"
---
# <a name="introduction-to-azure-security"></a>Introduktion till Azure-säkerhet
## <a name="overview"></a>Översikt
Vi vet att säkerhet är ett jobb i molnet och hur viktigt det är att du hitta korrekt och snabb information om Azure-säkerhet. En av de bästa skälen att använda Azure för dina program och tjänster är att dra nytta av dess mängd säkerhetsverktyg och-funktioner. Dessa verktyg och funktioner för att göra det möjligt att skapa säkra lösningar för säkra Azure-plattformen. Microsoft Azure tillhandahåller sekretess, integritet och tillgänglighet av kundinformation, samtidigt som transparent ansvarstagande.

För att hjälpa dig att bättre förstå insamling av säkerhet skrivs säkerhetskontrollerna i Microsoft Azure från både kunden och Microsoft operations perspektiv, detta white paper ”introduktion till Azure-säkerhet”, för att tillhandahålla en omfattande titt säkerhet som är tillgängliga med Microsoft Azure.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en offentlig molntjänstplattform som har stöd för ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Det kan köra Linux-behållare med Docker-integrering skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; Bygg serverdelar för iOS, Android och Windows enheter.

Azures offentliga molntjänster stöd för samma teknik som miljontals utvecklare och IT-proffs redan använder och litar på. När du bygger på, eller migrerar IT-tillgångar till en offentlig molntjänstleverantör du lita på den organisationens förmåga att skydda dina program och data med tjänsterna och kontroller får för att hantera säkerheten för dina molnbaserade tillgångar.

Azures infrastruktur är utformad från anläggning till program som värd för miljoner kunder samtidigt, och ger en säker grund som företaget kan möta sina säkerhetskrav.

Dessutom ger Azure dig en mängd olika konfigurerbara säkerhetsalternativ samt möjligheten att kontrollera dem så att du kan anpassa säkerheten för att uppfylla de specifika behoven för din organisations distributioner. Det här dokumentet hjälper dig att förstå hur Azure security funktioner kan hjälpa dig att uppfylla dessa.

> [!Note]
> Primärt fokus för det här dokumentet finns på kundinriktade kontroller som du kan använda för att anpassa och öka säkerheten för dina program och tjänster.
>
> Vi uppge viss översiktsinformation, men detaljerad information om hur Microsoft skyddar själva Azure-plattformen finns i informationen i den [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

### <a name="abstract"></a>Abstrakt
Till en början har offentliga molnmigreringar drivs av kostnadsbesparingar och smidighet till innovationer. Security ansågs av stor betydelse för en stund och även en show proppen för offentliga moln-migrering. Offentliga molnsäkerhet har övergått från en stor utmaning till någon av drivrutinerna för migreringen till molnet. Tanken bakom det är stora leverantörer av offentliga molntjänster överlägsen förmåga att skydda program och data för molnbaserade tillgångar.

Allt i Azures infrastruktur, från anläggning till tillämpningar, är utformat för att fungera som värd för miljoner kunder samtidigt, och den tillhandahåller en säker grund som företaget kan använda sig av för att möta de interna säkerhetsbehoven. Dessutom erbjuder Azure dig en mängd olika konfigurerbara säkerhetsalternativ och möjligheten att kontrollera dem så att du kan anpassa säkerheten för att uppfylla de specifika behoven hos dina distributioner att uppfylla IT principer för åtkomstkontroll och följa externa föreskrifter.

Det här dokumentet beskriver Microsofts strategi för säkerhet i Microsoft Azure cloud-plattformen:
* Säkerhetsfunktioner som implementeras av Microsoft för att skydda den Azure-infrastrukturen, kunddata och program.
* Azure-tjänster och säkerhetsfunktioner som är tillgängliga för dig att hantera säkerheten för tjänsterna och dina data i dina Azure-prenumerationer.

## <a name="summary-azure-security-capabilities"></a>Översikt över säkerhetsfunktionerna i Azure
Följande tabell ger en kort beskrivning av säkerhetsfunktioner som implementeras av Microsoft för att skydda Azure-infrastrukturen, kunddata och säkra program.
### <a name="security-features-implemented-to-secure-the-azure-platform"></a>Säkerhetsfunktioner för att skydda Azure-plattformen:
De funktioner som visas i följande är funktioner som du kan granska för att tillhandahålla säker på att Azure-plattformen hanteras på ett säkert sätt. Länkar har angetts för ytterligare gå nedåt i hur Microsoft behandlar kundfrågor förtroende i fyra områden: Säker plattform, sekretess och kontroller, efterlevnad och transparens.


| [Säker plattform](https://www.microsoft.com/en-us/trustcenter/Security/default.aspx)  | [Sekretess och kontroller](https://www.microsoft.com/en-us/trustcenter/Privacy/default.aspx)  |[Efterlevnad](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)   | [Transparens](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Security utvecklingscykeln](https://www.microsoft.com/en-us/sdl/), interna granskningar | [Hantera dina data hela tiden](https://www.microsoft.com/en-us/trustcenter/Privacy/You-own-your-data) | [Säkerhetscenter](https://www.microsoft.com/en-us/trustcenter/default.aspx) |[Hur Microsoft skyddar kundinformation i Azure-tjänster](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx) |
| [Obligatorisk säkerhetsutbildning, bakgrundskontroller](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Styra på plats](https://www.microsoft.com/en-us/trustcenter/Privacy/Where-your-data-is-located) |  [Vanliga kontroller Hub](https://www.microsoft.com/en-us/trustcenter/Common-Controls-Hub) |[Hur Microsoft hanterar plats i Azure-tjänster](http://azuredatacentermap.azurewebsites.net/)|
| [Penetrationstester](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx), [intrångsidentifiering, DDoS](https://www.microsoft.com/en-us/trustcenter/Security/ThreatManagement), [granskningar & loggning](https://www.microsoft.com/en-us/trustcenter/Security/AuditingAndLogging) | [Ge åtkomst till data på dina villkor](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Molntjänster på grund av noggrannhet Checklista](https://www.microsoft.com/en-us/trustcenter/Compliance/Due-Diligence-Checklist) |[Som Microsoft kan komma åt dina data på vilka villkor](https://www.microsoft.com/en-us/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Avancerade Datacenter](https://www.microsoft.com/en-us/cloud-platform/global-datacenters), fysisk säkerhet [säkra nätverk](https://docs.microsoft.com/azure/security/security-network-overview) | [Svara till brottsbekämpande myndigheter](https://www.microsoft.com/en-us/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Efterlevnad av tjänst, plats & bransch](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx) |[Hur Microsoft skyddar kundinformation i Azure-tjänster](https://www.microsoft.com/en-us/trustcenter/Transparency/default.aspx)|
|  [Security Incident response](https://aka.ms/SecurityResponsepaper), [delat ansvar](https://aka.ms/sharedresponsibility) |[Strikta sekretesstandarder](https://www.microsoft.com/en-us/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Granska certifiering för Azure-tjänster, transparens hub](https://www.microsoft.com/en-us/trustcenter/Compliance/default.aspx)|



### <a name="security-features-offered-by-azure-to-secure-data-and-application"></a>Säkerhetsfunktioner som erbjuds av Azure för att skydda Data och program
Beroende på tjänsten molnmodell finns variabeln ansvar för vem som ansvarar för att hantera säkerheten för programmet eller tjänsten. Det finns funktioner som är tillgängliga i Azure-plattformen för att hjälpa dig att uppfylla dessa uppgifter via de inbyggda funktionerna och via partnerlösningar som kan distribueras till en Azure-prenumeration.

De inbyggda funktionerna ordnas i sex (6) huvudområden: Åtgärder, program, lagring, nätverk, beräkning och identitet. Mer information om hur funktionerna och egenskaperna som är tillgängliga i Azure-plattformen inom följande områden med sex (6) sker via sammanfattningsinformation.

## <a name="operations"></a>Åtgärder
Det här avsnittet innehåller ytterligare information om viktiga funktioner i säkerhetsåtgärder och översiktlig information om dessa funktioner.

### <a name="security-and-audit-dashboard"></a>Säkerhet och granskning instrumentpanel
Den [säkerhet och Granskningslösning](https://docs.microsoft.com/azure/operations-management-suite/oms-security-getting-started) ger en heltäckande översikt över din organisations IT-säkerhetsstatus med [inbyggda sökfrågor](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) för viktiga problem som kräver din uppmärksamhet. Den [säkerhet och granskning](https://technet.microsoft.com/library/mt484091.aspx) instrumentpanelen är startsidan för allt som rör säkerhet i Log Analytics. Det ger en övergripande inblick i säkerhetstillståndet hos dina datorer. Här kan du också visa alla händelser från de senaste 24 timmarna, sju dagarna eller ett annat anpassat tidsintervall.

Du kan dessutom konfigurera säkerhet och efterlevnad av [automatiskt utföra specifika åtgärder](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) när en viss händelse identifieras.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager ](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) kan du arbeta med resurserna i din lösning som en grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en [Azure Resource Manager-mall](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) för distribution och mallen kan användas i olika miljöer, till exempel testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

Azure Resource Manager-mall-baserade distributioner hjälp oss att förbättra säkerheten för lösningar som distribueras i Azure eftersom Standardsäkerhet styra inställningar och kan integreras i standardiserad mallbaserade distributioner. Detta minskar risken för konfigurationsfel för säkerhet som kan äga rum under manuella distributioner.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utökningsbar tjänst Application Performance Management (APM) för webbutvecklare. Med Application Insights kan du övervaka dina live-webbprogram och identifiera automatiskt prestandaavvikelser. Den inkluderar kraftfulla analysverktyg att hjälpa dig att diagnostisera problem och förstå vad användare faktiskt gör med dina appar. Det övervakar programmets hela tiden som den körs, både under testning och när du har publicerat eller distribuerat den.

Application Insights skapar diagram och tabeller som visar dig, till exempel, vilka tidpunkter på dagen du får de flesta användare, hur responsiv appen är och hur väl den hanteras av alla externa tjänster som den är beroende av.

Om det finns krascher, fel eller prestandaproblem kan söka du igenom dessa data i detalj för att diagnostisera orsaken. Och tjänsten skickar du e-postmeddelanden om det finns ändringar i tillgänglighet och prestanda för din app. Application Insights blir därmed en värdefull säkerhetsverktyget eftersom det hjälper tillgänglighet i sekretess, integritet och tillgänglighet security triad.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) erbjuder visualisering, fråga, routning, aviseringar, Automatisk skala och automatisering på data både från Azure-infrastrukturen ([aktivitetsloggen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)) och varje enskild Azure-resurs ([diagnostik Loggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)). Du kan använda Azure Monitor för att varna dig om säkerhetsrelaterade händelser som genereras i Azure-loggar.

### <a name="log-analytics"></a>Log Analytics
[Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/) – innehåller en IT-hanteringslösningen för både lokalt och från tredje part molnbaserad infrastruktur (till exempel AWS) utöver Azure-resurser. Data från Azure Monitor kan dirigeras direkt till Log Analytics så att du kan se mått och loggar i hela din miljö på samma ställe.

Log Analytics kan vara användbart i kriminaltekniska och andra säkerhetsanalys som verktyget gör det möjligt att snabbt söka igenom stora mängder säkerhetsrelaterade transaktioner med en metod för flexibla frågor. Dessutom kan den lokala [loggar för brandväggen och proxyservern kan exporteras till Azure och göras tillgängliga för analys med hjälp av Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-proxy-firewall)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](https://docs.microsoft.com/azure/advisor/) är en personligt anpassad molnrådgivare som hjälper dig att optimera dina Azure-distributioner. Den analyserar din resurskonfiguration och användningstelemetri. Den rekommenderar sedan lösningar för att förbättra den [prestanda](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), [security](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations), och [hög tillgänglighet](https://docs.microsoft.com/azure/advisor/advisor-high-availability-recommendations) för dina resurser vid sökning efter möjligheter att [minska de totala Azure spendera](https://docs.microsoft.com/azure/advisor/advisor-cost-recommendations). Azure Advisor tillhandahåller säkerhetsrekommendationer som förbättrar din övergripande säkerhetsposition för lösningar som du distribuerar i Azure. De här rekommendationerna hämtas från säkerhetsanalys som utförs av [Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-intro)

### <a name="azure-security-center"></a>Azure Security Center
Med hjälp av [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Dessutom hjälper Azure Security Center med säkerhetsåtgärder genom att ge dig en enda instrumentpanel som hämtar aviseringar och rekommendationer som kan åtgärdas omedelbart. Ofta kan du åtgärda problem med ett enda klick i Azure Security Center-konsolen.
## <a name="applications"></a>Appar
Avsnittet innehåller ytterligare information om viktiga funktioner i programmet säkerhets- och sammanfattning information om dessa funktioner.

### <a name="web-application-vulnerability-scanning"></a>Web Application granskar säkerhetsrisker
En av de enklaste sätten att komma igång med testa efter säkerhetsrisker på din [App Service-app](https://docs.microsoft.com/azure/app-service/overview) är att använda den [integrering med Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) att utföra ett klick granskar säkerhetsrisker i din app. Du kan visa testresultaten i en rapport som lätt att förstå och lär dig att åtgärda sårbarheten med stegvisa instruktioner.

### <a name="penetration-testing"></a>Penetrationstester
Om du vill utföra egna test för penetrationstester eller vill använda en annan skanner suite eller providern, måste du följa den [Azure penetrationstest godkännandeprocess](https://docs.microsoft.com/azure/security/azure-security-pen-testing ) och hämta förhandsgodkännande för att utföra önskade penetrationstester testerna.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
Brandväggen för webbaserade program (WAF) i [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) skyddar webbprogram från vanliga webbaserade attacker som SQL injection, cross-site skriptattacker och sessionskapning. Den levereras förkonfigurerad med skydd mot hot som identifieras av den [OWASP Open Web Application Security Project () som de översta 10 vanligaste sårbarheterna](https://msdn.microsoft.com/library/).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service
[App Service-autentisering / auktorisering](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) är en funktion som gör det möjligt för programmet att logga in användare så att du inte behöver ändra koden på app-serverdel. Det ger ett enkelt sätt att skydda ditt program och arbeta med data per användare.

### <a name="layered-security-architecture"></a>Arkitektur med flernivåsäkerhet
Eftersom [App Service-miljöer](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-intro) ger en isolerad körningsmiljö som distribuerats i en [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview), utvecklare kan skapa en arkitektur med flernivåsäkerhet att tillhandahålla olika nätverksåtkomst för varje applikationsnivå. En vanligt önskemål är att dölja API-servrar från Allmänt Internetåtkomst och endast tillåta API: er anropas av överordnade webbappar. [Nätverkssäkerhetsgrupper (NSG)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) kan användas på Azure Virtual Network-undernät som innehåller App Service-miljöer för att begränsa offentlig åtkomst till API-program.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Web serverdiagnostik- och programdiagnostik
App Service web apps ger diagnostisk funktionalitet för att logga information från både webbservern och webbprogrammet. Dessa logiskt är indelade i [web serverdiagnostik](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) och [programdiagnostik](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webbservern innehåller två större utvecklingen i diagnostisera och felsöka webbplatser och program.

Den första nya funktionen är i realtid statusinformation om programpooler, arbetsprocesser, platser, domäner för programmet och begäranden körs. Andra nya fördelarna är de detaljerade spårningshändelser som spårar en begäran under hela processen slutförts och svaret.

Om du vill aktivera insamling av dessa spårningshändelser kan IIS 7 konfigureras för att automatiskt samla in fullständiga spårningsloggarna i XML-format, för alla viss begäran baserat på tidsåtgång eller felkoder för svaret.

#### <a name="web-server-diagnostics"></a>Web serverdiagnostik
Du kan aktivera eller inaktivera följande typer av loggar:

-   Detaljerad felloggning - detaljerad information om fel för HTTP-statuskoder som indikerar ett fel (statuskod 400 eller större). Detta kan innehålla information som hjälper dig att avgöra varför servern returnerade felkoden.

-   Spårning av misslyckade begäranden – detaljerad information om misslyckade förfrågningar, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Detta kan vara användbart om du vill öka platsprestanda eller isolera vad som orsakar ett specifikt HTTP-fel som ska returneras.

-   Web Server-loggning – Information om hur du använder W3C utökat loggfilsformat för HTTP-transaktioner. Detta är användbart när du fastställer övergripande platsmått, till exempel antalet begäranden som hanteras eller hur många begäranden som kommer från en specifik IP-adress.

#### <a name="application-diagnostics"></a>Programdiagnostik
[Programdiagnostik](https://docs.microsoft.com/azure/app-service/troubleshoot-diagnostic-logs) kan du samla in information som genereras av ett webbprogram. ASP.NET-program kan använda den [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) klassen för att logga information till programloggen för diagnostik. I Application Diagnostics finns det två huvudtyper för händelser, de som är relaterade till programprestanda och de som är relaterade till programfel. Fel och problem kan delas upp ytterligare i anslutningen, säkerhet och fel. Problem med är ofta relaterad till problem med programkoden.

I Programdiagnostik kan visa du gruppera händelser på följande sätt:

-   Alla (visar alla händelser)
-   Programfel (visar undantagshändelser)
-   Prestanda (visar prestandahändelser)

## <a name="storage"></a>Storage
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure storage-säkerhet och sammanfattning information om dessa funktioner.

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan skydda ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC). Begränsa åtkomst baserat på den [behöver veta](https://en.wikipedia.org/wiki/Need_to_know) och [lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är mycket viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Dessa behörigheter beviljas genom att tilldela lämpliga RBAC-roll till grupper och program för ett visst omfång. Du kan använda [inbyggda RBAC-roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles), till exempel Lagringskontodeltagare att tilldela behörigheter till användare. Åtkomst till lagringen nycklar för ett storage-konto med den [Azure Resource Manager](https://docs.microsoft.com/azure/storage/storage-security-guide) modellen kan styras via rollbaserad åtkomstkontroll (RBAC).

### <a name="shared-access-signature"></a>Signatur för delad åtkomst
En [signatur för delad åtkomst (Shared Access Signature, SAS)](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1) ger delegerad åtkomst till resurser på ditt lagringskonto. SAS innebär att du kan ge en klient begränsad behörighet till objekt i ditt storage-konto för en angiven tidsperiod och med en angiven uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för kontot.

### <a name="encryption-in-transit"></a>Kryptering under överföring
Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Med Azure Storage kan skydda du data med hjälp av:
-   [Kryptering på transportnivå](https://docs.microsoft.com/azure/storage/storage-security-guide#encryption-in-transit), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Koppla kryptering](https://docs.microsoft.com/azure/storage/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), till exempel [SMB 3.0-kryptering](https://docs.microsoft.com/azure/storage/storage-security-guide) för [Azure-filresurser](https://docs.microsoft.com/azure/storage/storage-dotnet-how-to-use-files).

-   Client side encryption, att kryptera data innan den överförs till lagring och för att dekryptera data efter överföringen slut på minne.

### <a name="encryption-at-rest"></a>Vilande kryptering
Många organisationer är ett obligatoriskt steg mot datasekretess, efterlevnad och datasuveränitet i datakryptering i viloläge. Det finns tre Azure storage-säkerhetsfunktioner som tillhandahåller kryptering av data som är ”i vila”:

-   [Kryptering av lagringstjänst](https://docs.microsoft.com/azure/storage/storage-service-encryption) kan du begära att lagringstjänsten automatiskt kryptera data när du skriver den till Azure Storage.

-   [Client side Encryption](https://docs.microsoft.com/azure/storage/storage-client-side-encryption) tillhandahåller även funktionen för kryptering i vila.

-   [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) kan du kryptera OS-diskar och datadiskar som används av en virtuell IaaS-dator.

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller mätvärden för ett lagringskonto. Du kan använda dessa data för att spåra förfrågningar, analysera användningstrender och diagnostisera problem med lagringskontot. Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och diagnostisera problem med en lagringstjänst. Förfrågningar loggas på basis av bästa prestanda. Följande typer av autentiserade begäranden loggas:
-   Lyckade begäranden.

-   Misslyckade förfrågningar, inklusive timeout, begränsning, nätverk, auktorisering och andra fel.

-   Begäranden med hjälp av en signatur för delad åtkomst (SAS), inklusive misslyckade och lyckade förfrågningar.

-   Begäranden till analytics-data.

### <a name="enabling-browser-based-clients-using-cors"></a>Aktivera webbläsarbaserade klienter med CORS
[Cross-Origin Resource Sharing (CORS)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) är en mekanism som ger domäner ge behörighet för åtkomst till varandras resurser till varandra. Användaragenten skickar extra meddelandehuvuden att JavaScript-koden som lästs in från en viss domän har behörighet att komma åt resurser som finns på en annan domän. Den senare domänen svarar den sedan med extra meddelandehuvuden att tillåta eller neka den ursprungliga domän har åtkomsten till dess resurser.

Azure storage-tjänster stöder nu CORS så att när du har angett CORS-regler för tjänsten en korrekt autentiserad begäran som görs mot tjänsten från en annan domän ska utvärderas för att avgöra om den är tillåten enligt de regler som du har angett.
## <a name="networking"></a>Nätverk
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure network security och sammanfattning information om dessa funktioner.

### <a name="network-layer-controls"></a>Layer nätverkskontroller
Åtkomstkontroll för nätverk syftar till att begränsa anslutning till och från specifika enheter eller undernät och representerar kärnan i nätverkssäkerhet. Målet med åtkomstkontrollen för nätverk är att se till att dina virtuella datorer och tjänster är tillgängliga så att endast användare och enheter som du vill ha dem tillgängliga.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En [Nätverkssäkerhetsgrupp (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) är en grundläggande tillståndskänsliga paket filtrering brandväggen och det låter dig kontrollera åtkomst baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG: er ger inte programmet layer inspektion eller autentiserad åtkomstkontroller. De kan användas för att styra trafik som flyttar mellan undernät inom ett Azure-nätverk och trafik mellan ett Azure-nätverk och Internet.

#### <a name="route-control-and-forced-tunneling"></a>Dirigera kontroll och Tvingad tunneltrafik
Möjlighet att styra routning beteende på dina virtuella Azure-nätverk är funktionen för kontroll av säkerhets- och ett kritiskt. Om du vill se till att all trafik till och från Azure Virtual Network går igenom den virtuella säkerhetsapparat måste du kunna styra och ändra routning beteende. Du kan göra detta genom att konfigurera användardefinierade vägar i Azure.

[Användardefinierade vägar](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) kan du anpassa inkommande och utgående sökvägar för trafik som flyttas till och från enskilda virtuella datorer eller undernät för att försäkra dig om mest säkra vägen möjligt. [Tvingad tunneltrafik](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts att initiera en anslutning till enheter på Internet.

Detta skiljer sig från att kunna acceptera inkommande anslutningar och sedan svara på dem. Frontend-webbservrar måste svara på begäranden från Internet-värdar och så Internet källkod trafik tillåts inkommande till dessa servrar och webbservrar kan svara.

Tvingad tunneltrafik används ofta för att tvinga utgående trafik till Internet för att gå igenom en lokal säkerhet proxyservrar och brandväggar.

#### <a name="virtual-network-security-appliances"></a>Virtuella funktioner för nätverkssäkerhet
Nätverkssäkerhetsgrupper, användardefinierade vägar och Tvingad tunneltrafik tillhandahåller en säkerhetsnivå för Nätverks- och transport-lager av den [OSI-modell](https://en.wikipedia.org/wiki/OSI_model), det kan finnas tillfällen när du vill aktivera säkerhet på högre nivåer i den stack. Du kan komma åt de här förbättrade funktioner för nätverkssäkerhet med hjälp av en Azure installation partnernätverkslösningar. Du hittar det senaste Azure-partner-nätverket säkerhetslösningar genom att besöka den [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söka efter ”säkerhet” och ”nätverkssäkerhet”.

### <a name="azure-virtual-network"></a>Azure Virtual Network

Ett virtuellt Azure-nätverk (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk avgränsning av Azure nätverksresurserna som är anpassad efter din prenumeration. Du kan helt styra IP-adressblocken, DNS-inställningarna, säkerhetsprinciperna och routingtabellerna inom det här nätverket. Du kan segmentera ditt VNet i undernät och placerar Azure IaaS-datorer (VM) och/eller [molntjänster (PaaS rollinstanser)](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me) på virtuella Azure-nätverk.

Du kan dessutom ansluta det virtuella nätverket till ditt lokala nätverk med ett av [anslutningsalternativen](https://docs.microsoft.com/azure/vpn-gateway/) som finns i Azure. I princip kan du expandera ditt nätverk till Azure, med fullständig kontroll över IP-adressblock och de fördelar som Azure på företagsnivå erbjuder.

Azure-nätverk stöder olika scenarier för säker fjärråtkomst. Några av dessa är:

-   [Ansluta enskilda arbetsstationer till Azure Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

-   [Ansluta lokala nätverk till ett virtuellt Azure-nätverk med en VPN-anslutning](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

-   [Ansluta lokala nätverk till ett virtuellt Azure-nätverk med en dedikerad WAN-länk](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)

-   [Ansluta virtuella Azure-nätverk till varandra](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps)

### <a name="vpn-gateway"></a>VPN Gateway
Om du vill skicka nätverkstrafik mellan ett Azure-nätverk och lokala platser, måste du skapa en VPN-gateway för Azure Virtual Network. En [VPN-gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) är en typ av virtuell nätverksgateway som skickar krypterad trafik via en offentlig anslutning. Du kan också använda VPN-gatewayer för att skicka trafik mellan Azure-nätverk över Azure nätverksresurserna.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) är en dedikerad WAN-länken som låter dig Utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör.

![Express Route](./media/azure-security/azure-security-fig1.png)

Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar går inte via offentliga Internet och därför kan anses vara säkrare än VPN-baserade lösningar. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) tillhandahåller en [Application Delivery Controller (ADC)](https://en.wikipedia.org/wiki/Application_delivery_controller) som en tjänst som erbjuder olika layer 7-belastningsutjämningsfunktioner för ditt program.

![Application Gateway](./media/azure-security/azure-security-fig2.png)

Det hjälper dig att optimera webbservergruppens produktivitet genom att avlasta CPU-intensiv SSL avslutning till Application Gateway (även kallat ”SSL-avlastning” eller ”SSL-bryggning”). Här finns även andra Layer 7-routningsfunktioner inklusive resursallokeringsdistribution av inkommande trafik, Cookiebaserad sessionstillhörighet, URL-sökvägsbaserad Routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. Azure Application Gateway är en Layer 7-lastbalanserare.

Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt.

Programmet tillhandahåller många Application Delivery Controller (ADC)-funktioner, inklusive HTTP läsa in belastningsutjämning, cookie-baserad sessionstillhörighet [Secure Sockets Layer (SSL)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-powershell) avlastning, anpassade hälsoavsökningar, stöd för flera platser och många andra.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
Brandvägg för webbaserade program är en funktion i [Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) som ger skydd till webbprogram som utnyttjar Programgateway för standard Application Delivery Control (ADC)-funktioner. Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de 10 vanligaste OWASP-säkerhetsproblemen.

![Brandvägg för webbaserade program](./media/azure-security/azure-security-fig1.png)

-   Skydd mot SQL-inmatning

-   Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

-   Skydd mot åtgärder som inte följer HTTP-protokollet

-   Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

-   Skydd mot robotar, crawlers och skannrar

-   Identifiering av vanliga felkonfigureringar för programmet (det vill säga Apache, IIS osv.)


En centraliserad brandvägg mot webbattacker underlättar säkerhetshanteringen och håller programmet bättre skyddat mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en programgateway med brandvägg för webbprogram.
### <a name="traffic-manager"></a>Traffic Manager
Microsoft [Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) låter dig styra distributionen av användartrafik för tjänstslutpunkter i olika datacenter. Tjänstslutpunkter som stöds av Traffic Manager är virtuella Azure-datorer, Web Apps och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte tillhör Azure. Traffic Manager använder Domain Name System (DNS) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en [trafikdirigeringsmetoden](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) och hälsotillståndet för slutpunkterna.

Traffic Manager erbjuder en uppsättning trafikroutningsmetoder efter programbehov för olika, slutpunktshälsa [övervakning](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring), och automatisk redundans. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.
### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) levererar hög tillgänglighet och nätverksprestanda till dina program. Det är en belastningsutjämnare för Layer-4 (TCP, UDP) som distribuerar inkommande trafik mellan felfria instanser av tjänster som definierats i en belastningsutjämnad uppsättning. Azure Load Balancer kan konfigureras för att:

-   Läsa in belastningsutjämna inkommande Internettrafik till virtuella datorer. Den här konfigurationen kallas [belastningsutjämning för Internet-riktade](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Belastningsutjämna trafik mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett virtuellt nätverk med flera platser. Den här konfigurationen kallas [intern belastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview). 

- Vidarebefordra extern trafik till en specifik virtuell dator

### <a name="internal-dns"></a>Internt DNS
Du kan hantera listan över DNS-servrar som används i ett virtuellt nätverk i hanteringsportalen eller i nätverkskonfigurationsfilen. Kunder kan lägga till upp till 12 DNS-servrar för varje virtuellt nätverk. När du anger DNS-servrar, är det viktigt att verifiera att du lista kundens DNS-servrar i rätt ordning för kundens miljö. DNS-serverlistor fungerar inte (round-robin). De används i den ordning som de anges. Om den första DNS-servern i listan är går att nå använder klienten den DNS-servern, oavsett om DNS-servern fungerar korrekt eller inte. Ändra ordningen för DNS-server för kundens virtuella nätverk, ta bort DNS-servrar från listan och lägga till dem i ordningen som kunden vill. DNS stöder tillgänglighet aspekt av ”CIA” security triad.

### <a name="azure-dns"></a>Azure DNS
Den [Domain Name System](https://technet.microsoft.com/library/bb629410.aspx), eller DNS, ansvarar för att översätta (eller lösa) namnet på en webbplats eller tjänst till dess IP-adress. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. DNS stöder tillgänglighet aspekt av ”CIA” security triad.
### <a name="log-analytics-nsgs"></a>Log Analytics NSG: er
Du kan aktivera följande diagnostiklogg kategorier för NSG: er:
-   Händelse: Innehåller poster för vilka NSG reglerna tillämpas på virtuella datorer och instansroller som baseras på MAC-adress. Status för de här reglerna som samlas in var 60: e sekund.

-   Regler för räknare: Innehåller poster för hur många gånger varje NSG-regel används för att neka eller tillåta trafik.

### <a name="azure-security-center"></a>Azure Security Center
Security Center hjälper dig att förhindra, upptäcka och svara på hot, och ger dig ökad insyn i och kontroll över säkerheten hos dina Azure-resurser. Den tillhandahåller integrerad säkerhetsövervakning och principhantering säkerhetshantering för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar. Nätverkscenter rekommendationer kring brandväggar, Nätverkssäkerhetsgrupper, konfigurera regler för inkommande trafik och mycket mer.

Tillgängliga nätverksrekommendationer är följande:

-   [Lägg till en nästa generations brandvägg](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall) rekommenderar att du lägger till en nästa Generation Firewall (NGFW) från en Microsoft-partner att öka din säkerhetsskydd

-   [Dirigera trafiken endast via NGFW](https://docs.microsoft.com/azure/security-center/security-center-add-next-generation-firewall#route-traffic-through-ngfw-only) rekommenderar att du konfigurerar network regler för nätverkssäkerhetsgrupper (NSG) som tvingar inkommande trafik till den virtuella datorn via din NGFW.

-   [Aktivera Nätverkssäkerhetsgrupper på undernät eller virtuella datorer](https://docs.microsoft.com/azure/security-center/security-center-enable-network-security-groups) rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer.

-   [Begränsa åtkomst via slutpunkt mot Internet](https://docs.microsoft.com/azure/security-center/security-center-restrict-access-through-internet-facing-endpoints) rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er.


## <a name="compute"></a>Compute

Avsnittet innehåller ytterligare information om viktiga funktioner i det här området och sammanfattning information om dessa funktioner.

### <a name="antimalware--antivirus"></a>Program mot skadlig kod & Antivirus
Med Azure IaaS, kan du använda program mot skadlig kod från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro, McAfee och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot. [Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) för Azure Cloud Services och virtuella datorer är en protection-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware innehåller konfigurerbara aviseringar när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på din Azure-system. Microsoft Antimalware kan också distribueras med hjälp av Azure Security Center

### <a name="hardware-security-module"></a>Modul för maskinvarusäkerhet
Kryptering och autentisering du inte förbättra säkerheten nycklarna själva som är skyddade. Du kan förenkla hanteringen av och säkerheten för viktiga hemligheter och nycklar genom att lagra dem i [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis). Key Vault ger dig möjlighet att lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Din SQL Server krypteringsnycklar för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Key Vault med andra nycklar eller hemligheter från dina program. Behörigheter och åtkomst till de skyddade objekten hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuell dator
[Azure Backup](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) är en lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och mänskliga faktorn kan införa buggar i dina program som kan leda till säkerhetsproblem. Dina virtuella datorer som kör Windows och Linux är skyddade med Azure Backup.

### <a name="azure-site-recovery"></a>Azure Site Recovery
En viktig del av din organisations [företag affärskontinuitet och haveriberedskap (BCDR)](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) strategi är att räkna ut hur att företagets arbetsbelastningar och appar igång när planerade och oplanerade avbrott inträffar. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats kraschar.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Transparent datakryptering (TDE)](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault) och kolumnen filnivåkryptering (CLE) är SQL server-funktioner för kryptering. Den här typen av kryptering kräver kunder att hantera och lagra de kryptografiska nycklarna som du använder för kryptering.

Tjänsten Azure Key Vault (AKV) är utformad för att förbättra säkerhet och hantering av de här nycklarna på en säker och mycket tillgänglig plats. SQL Server Connector gör det möjligt för SQL Server att använda de här nycklarna från Azure Key Vault.

Om du kör SQL Server med lokala datorer, finns steg som du kan följa för att få åtkomst till Azure Key Vault från en lokal SQL Server-dator. Men för SQL Server i virtuella Azure-datorer, kan du spara tid med hjälp av funktionen Azure Key Vault-integrering. Med några Azure PowerShell-cmdletar för att aktivera den här funktionen, kan du automatisera konfigurationen som krävs för en SQL-VM till ditt nyckelvalv.

### <a name="vm-disk-encryption"></a>VM-diskkryptering
[Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) är en ny funktion som hjälper dig att kryptera din Windows och Linux IaaS VM-diskar. Det gäller branschens standard BitLocker-funktion i Windows och DM-crypt i Linux för att kryptera volymer för Operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter i Key Vault-prenumerationen. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring.

### <a name="virtual-networking"></a>Virtuellt nätverk
Virtuella datorer behöver nätverksanslutning. För att stödja detta krav, kräver Azure virtuella datorer som ska anslutas till en Azure-nätverk. Azure Virtual Network är en logisk konstruktion som bygger på den fysiska Azure nätverksresurserna. Varje logisk [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) är isolerad från alla andra Azure-nätverk. Denna isolering hjälper att se till att nätverkstrafik i dina distributioner inte är tillgänglig för andra Microsoft Azure-kunder.

### <a name="patch-updates"></a>Patchuppdateringar
Patch-uppdateringar utgångspunkt för att identifiera och åtgärda eventuella problem och förenkla hanteringen för software update både genom att minska antalet programuppdateringar måste du distribuera i ditt företag och genom att öka din förmåga att övervaka efterlevnad.

### <a name="security-policy-management-and-reporting"></a>Hantering av säkerhetsprinciper och rapportering
[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) hjälper till att förhindra, upptäcka och svara på hot och ger dig ökad insyn i och kontroll över säkerheten hos dina Azure-resurser. Den tillhandahåller integrerad säkerhetsövervakning och principhantering säkerhetshantering för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

### <a name="azure-security-center"></a>Azure Security Center
Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

Skydda system, program och data börjar med identity-baserade åtkomstkontroller. Identitets- och hanteringsfunktioner som är inbyggda i Microsoft-produkter och tjänster att skydda din organisations- och personliga information från obehörig åtkomst när du gör den tillgänglig för behöriga användare var och när de behöver den.

### <a name="secure-identity"></a>Säker identitet
Microsoft använder flera säkerhetsrutiner och tekniker i sina produkter och tjänster för att hantera identitet och åtkomst.
-   [Multifaktorautentisering](https://azure.microsoft.com/services/multi-factor-authentication/) kräver att användare använder flera metoder för åtkomst, både lokalt och i molnet. Det ger stark autentisering med en rad enkla verifieringsalternativ samtidigt minnesalternativ användare med en enkel inloggningsprocess.

-   [Microsoft Authenticator](https://aka.ms/authenticator) tillhandahåller ett användarvänligt sätt för multi-Factor Authentication som fungerar både med Microsoft Azure Active Directory och Microsoft-konton och har stöd för wearables och fingeravtryck-baserade godkännanden.

-   [Obligatorisk lösenordspolicy](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) ökar du säkerheten för traditionella lösenord genom att införa längd och komplexitet, krav, Tvingad periodiska rotation och kontoutelåsning efter misslyckade autentiseringsförsök.

-   [Tokenbaserad autentisering](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) aktiverar autentisering via Azure Active Directory.

-   [Rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) gör det möjligt att bevilja åtkomst baserat på användarens tilldelade roll, vilket gör det enkelt att ge användare endast mängden åtkomst som de behöver för att utföra sina arbetsuppgifter. Du kan anpassa RBAC per organisationens affärsmodell och risktolerans.

-   [Integrerad Identitetshantering (hybrididentitet)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) låter dig behålla kontrollen över användarnas åtkomst på interna plattformar för datacenter och moln, skapar en enda användar-ID för autentisering och auktorisering för alla resurser.

### <a name="secure-apps-and-data"></a>Skydda appar och data
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), en omfattande identitets- och åtkomsthanteringsmolnlösning, kan du skydda åtkomsten till data i program på plats och i molnet och förenklar hanteringen av användare och grupper. Den kombinerar kärnkatalogstjänster, avancerad identitetsstyrning, säkerhets- och åtkomsthantering för program och gör det enkelt för utvecklare att bygga principbaserad Identitetshantering i sina appar. För att förbättra ditt Azure Active Directory kan du lägga till betalfunktioner med hjälp av Azure Active Directory Basic-, Premium P1- och Premium P2-versionerna.

| Kostnadsfria / vanliga funktioner     | Grundläggande funktioner    |Premium P1-funktioner |Premium P2-funktioner | Azure Active Directory Join – endast relaterade funktioner för Windows 10|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Katalogobjekt](https://docs.microsoft.com/azure/active-directory/active-directory-editions#directory-objects), [användare/grupphantering (Lägg till/Uppdatera/ta bort) / användarbaserad etablering, enhetsregistrering](https://docs.microsoft.com/azure/active-directory/active-directory-editions#usergroup-management-addupdatedelete-user-based-provisioning-device-registration), [enkel inloggning (SSO)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#single-sign-on-sso), [Self-Service lösenord Ändra för molnanvändare](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-change-for-cloud-users), [Anslut (synkroniseringsmotor som utökar lokala kataloger till Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-sync-engine-that-extends-on-premises-directories-to-azure-active-directory), [säkerhet / rapporter för användning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#securityusage-reports)       |   [Gruppbaserad åtkomsthantering / etablering](https://docs.microsoft.com/azure/active-directory/active-directory-editions#group-based-access-managementprovisioning), [Självbetjäning för återställning av lösenord för molnanvändare](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-reset-for-cloud-users), [företagsanpassning (inloggning inloggningssidor/Access Panel-anpassning)](https://docs.microsoft.com/azure/active-directory/active-directory-editions#company-branding-logon-pagesaccess-panel-customization), [ Programproxy](https://docs.microsoft.com/azure/active-directory/active-directory-editions#application-proxy), [SLA 99,9%](https://docs.microsoft.com/azure/active-directory/active-directory-editions#sla-999) |  [Självbetjäningsgrupp och Apphantering/Självbetjäning för programtillägg/dynamiska grupper](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-group), [Self-Service lösenord återställning/ändring/upplåsning med lokal tillbakaskrivning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#self-service-password-resetchangeunlock-with-on-premises-write-back), [Multi-Factor Autentisering (i molnet och lokalt (MFA-Server))](https://docs.microsoft.com/azure/active-directory/active-directory-editions#multi-factor-authentication-cloud-and-on-premises-mfa-server), [MIM CAL + MIM-servern](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mim-cal-mim-server), [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-editions#cloud-app-discovery), [Connect Health](https://docs.microsoft.com/azure/active-directory/active-directory-editions#connect-health), [Automatisk lösenordsförnyelse för gruppkonton](https://docs.microsoft.com/azure/active-directory/active-directory-editions#automatic-password-rollover-for-group-accounts)|   [Identitetsskydd](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection), [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)|  [Anslut en enhet till Azure AD, Desktop SSO, Microsoft Passport för Azure AD, Administrator Bitlocker-återställning](https://docs.microsoft.com/azure/active-directory/active-directory-editions#join-a-device-to-azure-ad-desktop-sso-microsoft-passport-for-azure-ad-administrator-bitlocker-recovery), [MDM-automatisk registrering, Self-Service Bitlocker-återställning, ytterligare lokala administratörer till Windows 10-enheter via Azure AD Ansluta till](https://docs.microsoft.com/azure/active-directory/active-directory-editions#mdm-auto-enrollment)|


- [Cloud App Discovery](https://docs.microsoft.com/azure/active-directory/active-directory-cloudappdiscovery-whatis) är en premiumfunktion i Azure Active Directory som gör det möjligt för dig att identifiera molnprogram som används av anställda i din organisation.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) är en säkerhetstjänst som använder Azure Active Directory avvikelseidentifiering funktioner för att tillhandahålla en konsoliderad vy över riskhändelser och potentiella problem som kan påverka din organisations identiteter.

- [Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta virtuella Azure-datorer till en domän utan att behöva distribuera domänkontrollanter. Användare logga in till de virtuella datorerna med sina företagsuppgifter för Active Directory och åtkomst till enkel resurser.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är högtillgänglig, global identitetshanteringstjänst för konsumentorienterade appar som kan skalas till hundratals miljoner identiteter och integreras över mobila plattformar och webbaserade plattformar. Kunderna kan logga in på alla dina appar via anpassningsbara upplevelser med befintliga konton i sociala medier eller skapa nya fristående autentiseringsuppgifter.

- [Azure Active Directory B2B-samarbete](https://aka.ms/aad-b2b-collaboration) är en säker partner CI-lösning som har stöd för dina företagsomfattande relationer genom att aktivera partners åtkomst till företagets program och data selektivt med hjälp av deras självhanterade identiteter.

- [Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) kan du utöka molnkapaciteten till Windows 10-enheter för central hantering. Det gör det möjligt för användarna att ansluta till företagets eller organisationens molnet via Azure Active Directory och förenklar åtkomsten till appar och resurser.

- [Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) tillhandahåller enkel inloggning och säker fjärråtkomst för webbprogram som hanteras lokalt.

## <a name="next-steps"></a>Nästa steg
- [Komma igång med Microsoft Azure-säkerhet](https://docs.microsoft.com/azure/security/azure-security-getting-started)

Azure-tjänster och -funktioner som du kan använda för att skydda dina tjänster och data i Azure

- [Azure Security Center](https://azure.microsoft.com/services/security-center/)

Förhindra, upptäck och svara på hot med ökad insyn i och kontroll över säkerheten hos dina Azure-resurser

- [Övervakning av säkerhetshälsa i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-monitoring)

Övervakningsfunktionerna i Azure Security Center att övervaka att fastställda principer.
