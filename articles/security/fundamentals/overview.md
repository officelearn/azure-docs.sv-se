---
title: Introduktion till Azure-säkerhet | Microsoft-dokument
description: Lär dig mer om Azure Security, dess tjänster och hur det fungerar.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/18/2019
ms.author: TomSh
ms.openlocfilehash: a3b738d95b5b777f1cf0329fb3c1bc3e2860421e
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545972"
---
# <a name="introduction-to-azure-security"></a>Introduktion till Azure-säkerhet
## <a name="overview"></a>Översikt
Vi vet att säkerhet är jobb ett i molnet och hur viktigt det är att du hittar korrekt och aktuell information om Azure-säkerhet. En av de bästa anledningarna till att använda Azure för dina program och tjänster är att dra nytta av dess breda utbud av säkerhetsverktyg och funktioner. Dessa verktyg och funktioner gör det möjligt att skapa säkra lösningar på den säkra Azure-plattformen. Microsoft Azure tillhandahåller sekretess, integritet och tillgänglighet för kunddata, samtidigt som transparent ansvarsskyldighet möjliggörs.

Den här artikeln innehåller en omfattande titt på den tillgängliga säkerheten med Azure.

### <a name="azure-platform"></a>Azure-plattformen
Azure är en offentlig molntjänstplattform som stöder ett brett urval av operativsystem, programmeringsspråk, ramverk, verktyg, databaser och enheter. Det kan köra Linux-behållare med Docker integration; skapa appar med JavaScript, Python, .NET, PHP, Java och Node.js; skapa back-ends för iOS-, Android- och Windows-enheter.

Offentliga Azure-molntjänster stöder samma teknik som miljontals utvecklare och IT-proffs redan förlitar sig på och litar på. När du bygger på eller migrerar IT-tillgångar till en offentlig molntjänstleverantör förlitar du dig på organisationens förmåga att skydda dina program och data med de tjänster och de kontroller de tillhandahåller för att hantera säkerheten för dina molnbaserade tillgångar.

Azures infrastruktur är utformad från anläggning till program för att vara värd för miljontals kunder samtidigt, och det ger en tillförlitlig grund på vilken företag kan uppfylla sina säkerhetskrav.

Dessutom ger Azure dig ett brett utbud av konfigurerbara säkerhetsalternativ och möjligheten att styra dem så att du kan anpassa säkerheten för att uppfylla de unika kraven i organisationens distributioner. Det här dokumentet hjälper dig att förstå hur Azure-säkerhetsfunktioner kan hjälpa dig att uppfylla dessa krav.

> [!Note]
> Det här dokumentet fokuserar främst på kundinriktade kontroller som du kan använda för att anpassa och öka säkerheten för dina program och tjänster.
>
> Information om hur Microsoft skyddar själva Azure-plattformen finns i [Azure Infrastructure security](infrastructure.md).

## <a name="summary-of-azure-security-capabilities"></a>Sammanfattning av Azure-säkerhetsfunktioner

### <a name="features-to-secure-the-azure-platform"></a>Funktioner för att skydda Azure-plattformen
Följande funktioner är funktioner som du kan granska för att ge en försäkran om att Azure-plattformen hanteras på ett säkert sätt. Länkar har tillhandahållits för ytterligare information om hur Microsoft hanterar frågor om kundförtroende på fyra områden: säker plattform, sekretess & kontroller, efterlevnad och transparens.

| [Säker plattform](https://www.microsoft.com/trustcenter/Security/default.aspx)  | [Sekretess & kontroller](https://www.microsoft.com/trustcenter/Privacy/default.aspx)  |[Efterlevnad](https://www.microsoft.com/trustcenter/Compliance/default.aspx)   | [Öppenhet](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| :-- | :-- | :-- | :-- |
| [Säkerhetsutvecklingscykel](https://www.microsoft.com/sdl/), Internrevisioner | [Hantera dina data hela tiden](https://www.microsoft.com/trustcenter/Privacy/You-own-your-data) | [Säkerhetscenter](https://www.microsoft.com/trustcenter/default.aspx) |[Så här skyddar Microsoft kunddata i Azure-tjänster](https://www.microsoft.com/trustcenter/Transparency/default.aspx) |
| [Obligatorisk säkerhetsutbildning, bakgrundskontroller](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) |  [Kontroll av dataplatsen](https://www.microsoft.com/trustcenter/Privacy/Where-your-data-is-located) |  [Hub för vanliga kontroller](https://www.microsoft.com/trustcenter/Common-Controls-Hub) |[Så här hanterar Microsoft dataplats i Azure-tjänster](https://azuredatacentermap.azurewebsites.net/)|
| [Penetrationstestning,](https://downloads.cloudsecurityalliance.org/star/self-assessment/StandardResponsetoRequestforInformationWindowsAzureSecurityPrivacy.docx) [intrångsdetektering, DDoS,](https://www.microsoft.com/trustcenter/Security/ThreatManagement) [Revisioner & loggning](https://www.microsoft.com/trustcenter/Security/AuditingAndLogging) | [Ge dataåtkomst på dina villkor](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms) |  [Checklistan för due diligence för molntjänster](https://www.microsoft.com/trustcenter/Compliance/Due-Diligence-Checklist) |[Vem i Microsoft kan komma åt dina data på vilka villkor](https://www.microsoft.com/trustcenter/Privacy/Who-can-access-your-data-and-on-what-terms)|
| [Toppmodernt datacenter,](https://www.microsoft.com/cloud-platform/global-datacenters)fysisk säkerhet, [säkert nätverk](network-overview.md) | [Svara på brottsbekämpning](https://www.microsoft.com/trustcenter/Privacy/Responding-to-govt-agency-requests-for-customer-data) |  [Efterlevnad per tjänst, plats & industri](https://www.microsoft.com/trustcenter/Compliance/default.aspx) |[Så här skyddar Microsoft kunddata i Azure-tjänster](https://www.microsoft.com/trustcenter/Transparency/default.aspx)|
|  [Hantering av säkerhetsincidenter](https://aka.ms/SecurityResponsepaper), [delat ansvar](https://aka.ms/sharedresponsibility) |[Stränga sekretessstandarder](https://www.microsoft.com/TrustCenter/Privacy/We-set-and-adhere-to-stringent-standards) |  | [Granska certifiering för Azure-tjänster, Transparency hub](https://www.microsoft.com/trustcenter/Compliance/default.aspx)|

### <a name="features-to-secure-data-and-application"></a>Funktioner för att skydda data och program
Beroende på molntjänstmodellen finns det ett varierande ansvar för vem som ansvarar för att hantera säkerheten för programmet eller tjänsten. Det finns funktioner som är tillgängliga i Azure-plattformen för att hjälpa dig att uppfylla dessa ansvarsområden genom inbyggda funktioner och genom partnerlösningar som kan distribueras till en Azure-prenumeration.

De inbyggda funktionerna är organiserade i sex funktionella områden: operationer, program, lagring, nätverk, beräkning och identitet. Ytterligare information om de funktioner som finns tillgängliga i Azure-plattformen i dessa sex områden tillhandahålls via sammanfattande information.

## <a name="operations"></a>Åtgärder
Det här avsnittet innehåller ytterligare information om viktiga funktioner i säkerhetsåtgärder och sammanfattande information om dessa funktioner.

### <a name="security-and-audit-dashboard"></a>Instrumentpanel för säkerhet och granskning
[Lösningen för säkerhet och granskning](../../security-center/security-center-intro.md) ger en omfattande vy över organisationens IT-säkerhetsposition med [inbyggda sökfrågor](https://blogs.technet.microsoft.com/msoms/2016/01/21/easy-microsoft-operations-management-suite-search-queries/) för anmärkningsvärda problem som kräver din uppmärksamhet. Instrumentpanelen [Säkerhet och granskning](https://technet.microsoft.com/library/mt484091.aspx) är startskärmen för allt som rör säkerhet i Azure Monitor-loggar. Det ger en inblick på hög nivå i säkerhetstillståndet för dina datorer. Här kan du också visa alla händelser från de senaste 24 timmarna, sju dagarna eller ett annat anpassat tidsintervall.

Dessutom kan du konfigurera security & compliance för att [automatiskt utföra specifika åtgärder](https://blogs.technet.microsoft.com/robdavies/2016/04/20/simple-look-at-oms-alert-remediation-with-runbooks-part-1/) när en viss händelse identifieras.

### <a name="azure-resource-manager"></a>Azure Resource Manager
[Med Azure Resource Manager](../../azure-resource-manager/management/deployment-models.md) kan du arbeta med resurserna i din lösning som grupp. Du kan distribuera, uppdatera eller ta bort alla resurser i lösningen i en enda, samordnad åtgärd. Du använder en [Azure Resource Manager-mall](https://blogs.technet.microsoft.com/canitpro/2015/06/29/devops-basics-infrastructure-as-code-arm-templates/) för distribution och den mallen kan fungera för olika miljöer som testning, mellanlagring och produktion. Resource Manager tillhandahåller säkerhets-, gransknings- och taggningsfunktioner som hjälper dig att hantera dina resurser efter distributionen.

Azure Resource Manager-mallbaserade distributioner hjälper till att förbättra säkerheten för lösningar som distribueras i Azure eftersom standardinställningar för säkerhetskontroll och kan integreras i standardiserade mallbaserade distributioner. Detta minskar risken för säkerhetskonfigurationsfel som kan uppstå under manuella distributioner.

### <a name="application-insights"></a>Application Insights
[Application Insights](https://docs.microsoft.com/azure/application-insights/) är en utökningsbar APM-tjänst (Application Performance Management) för webbutvecklare. Med Application Insights kan du övervaka dina live-webbprogram och automatiskt identifiera prestandaavvikelser. Den innehåller kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå vad användarna faktiskt gör med dina appar. Den övervakar ditt program hela tiden det körs, både under testning och efter att du har publicerat eller distribuerat det.

Application Insights skapar diagram och tabeller som visar dig, till exempel vilka tider på dagen du får flest användare, hur lyhörd appen är och hur väl den betjänas av externa tjänster som den är beroende av.

Om det finns krascher, fel eller prestandaproblem kan du söka igenom telemetridata i detalj för att diagnostisera orsaken. Och tjänsten skickar e-post om det finns några ändringar i tillgängligheten och prestanda för din app. Application Insight blir därmed ett värdefullt säkerhetsverktyg eftersom det hjälper till med tillgängligheten i sekretess, integritet och tillgänglighet säkerhet triad.

### <a name="azure-monitor"></a>Azure Monitor
[Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) erbjuder visualisering, fråga, routning, aviseringar, automatisk skalning och automatisering på data både från Azure-infrastrukturen ([Aktivitetslogg)](../../azure-monitor/platform/platform-logs-overview.md)och varje enskild Azure-resurs ([Diagnostikloggar](../../azure-monitor/platform/platform-logs-overview.md)). Du kan använda Azure Monitor för att varna dig om säkerhetsrelaterade händelser som genereras i Azure-loggar.

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar
[Azure Monitor-loggar](https://azure.microsoft.com/documentation/services/log-analytics/) – Tillhandahåller en IT-hanteringslösning för både lokal och molnbaserad infrastruktur från tredje part (till exempel AWS) utöver Azure-resurser. Data från Azure Monitor kan dirigeras direkt till Azure Monitor-loggar så att du kan se mått och loggar för hela din miljö på ett ställe.

Azure Monitor-loggar kan vara ett användbart verktyg i kriminaltekniska och andra säkerhetsanalyser, eftersom verktyget gör att du snabbt kan söka igenom stora mängder säkerhetsrelaterade poster med en flexibel frågemetod. Dessutom kan lokala [brandväggar och proxyloggar exporteras till Azure och göras tillgängliga för analys med hjälp av Azure Monitor-loggar.](../../log-analytics/log-analytics-agent-windows.md)

### <a name="azure-advisor"></a>Azure Advisor
[Azure Advisor](../../advisor/index.yml) är en anpassad molnkonsult som hjälper dig att optimera dina Azure-distributioner. Den analyserar din resurskonfiguration och användningstelemetri. Den rekommenderar sedan lösningar för att förbättra [prestanda,](../../advisor/advisor-performance-recommendations.md) [säkerhet](../../advisor/advisor-security-recommendations.md)och [hög tillgänglighet](../../advisor/advisor-high-availability-recommendations.md) för dina resurser samtidigt som du letar efter möjligheter att minska din [totala Azure-utgifter](../../advisor/advisor-cost-recommendations.md). Azure Advisor innehåller säkerhetsrekommendationer som avsevärt kan förbättra din övergripande säkerhetsposition för lösningar som du distribuerar i Azure. Dessa rekommendationer hämtas från säkerhetsanalys som utförs av [Azure Security Center.](../../security-center/security-center-intro.md)

### <a name="azure-security-center"></a>Azure Security Center
[Security Center](../../security-center/security-center-intro.md) hjälper dig att förebygga, identifiera och svara på hot med ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Härifrån kan du övervaka och hantera principer för alla Azureprenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Dessutom hjälper Security Center till säkerhetsåtgärder genom att tillhandahålla dig en enda instrumentpanel som visar aviseringar och rekommendationer som kan åtgärdas omedelbart. Ofta kan du åtgärda problem med ett enda klick i Security Center-konsolen.
## <a name="applications"></a>Program
Avsnittet innehåller ytterligare information om viktiga funktioner i programmets säkerhet och sammanfattande information om dessa funktioner.

### <a name="web-application-vulnerability-scanning"></a>Sårbarhetsskanning för webbprogram
Ett av de enklaste sätten att komma igång med testning av sårbarheter i [apptjänstappen](../../app-service/overview.md) är att använda [integreringen med Tinfoil Security](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) för att utföra sårbarhetsskanning med ett klick i din app. Du kan visa testresultaten i en lättförståelig rapport och lära dig hur du åtgärdar varje säkerhetsproblem med steg-för-steg-instruktioner.

### <a name="penetration-testing"></a>Penetrationstestning
Om du föredrar att utföra dina egna penetrationstester eller vill använda en annan scanner-svit eller-leverantör måste du följa [Azure-godkännandeprocessen för penetrationstestning](https://docs.microsoft.com/azure/security/fundamentals/pen-testing ) och få förhandsgodkännande för att utföra önskade penetrationstester.

### <a name="web-application-firewall"></a>Brandvägg för webbprogram
Brandväggen för webbprogram (WAF) i [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) hjälper till att skydda webbprogram från vanliga webbaserade attacker som SQL-injektion, skriptattacker över flera webbplatser och sessionskapning. Den levereras förkonfigurerad med skydd mot hot som identifierats av [OWASP (Open Web Application Security Project) som de 10 vanligaste sårbarheterna](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project).

### <a name="authentication-and-authorization-in-azure-app-service"></a>Autentisering och auktorisering i Azure App Service
[App Service Autentisering / auktorisering](../../app-service/overview-authentication-authorization.md) är en funktion som ger ett sätt för ditt program att logga in användare så att du inte behöver ändra kod på appen servering. Det är ett enkelt sätt att skydda ditt program och arbeta med data per användare.

### <a name="layered-security-architecture"></a>Säkerhetsarkitektur i flera lager
Eftersom [App Service-miljöer](../../app-service/environment/app-service-app-service-environment-intro.md) tillhandahåller en isolerad körningsmiljö som distribueras till ett [Virtuellt Azure-nätverk](../../virtual-network/virtual-networks-overview.md)kan utvecklare skapa en säkerhetsarkitektur i flera lager som ger olika nivåer av nätverksåtkomst för varje programnivå. En vanlig önskan är att dölja API-back-ends från allmän Internet-åtkomst och endast tillåta api:er som ska anropas av uppströms webbappar. [NSG (Network Security Groups)](https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/) kan användas i Azure Virtual Network-undernät som innehåller App Service-miljöer för att begränsa offentlig åtkomst till API-program.

### <a name="web-server-diagnostics-and-application-diagnostics"></a>Diagnostik och programdiagnostik för webbserver
App Service-webbappar tillhandahåller diagnostikfunktioner för loggningsinformation från både webbservern och webbprogrammet. Dessa är logiskt uppdelade i [webbserver diagnostik](../../app-service/troubleshoot-diagnostic-logs.md) och [program diagnostik](https://technet.microsoft.com/library/hh530058(v=sc.12).aspx). Webbservern innehåller två stora framsteg när det gäller att diagnostisera och felsöka webbplatser och program.

Den första nya funktionen är information om tillstånd i realtid om programpooler, arbetsprocesser, platser, programdomäner och begäranden som körs. Den andra nya fördelen är detaljerade spårningshändelser som spårar en begäran under hela processen för begäran och svar.

För att aktivera insamlingen av dessa spårningshändelser kan IIS 7 konfigureras för att automatiskt fånga in fullständiga spårningsloggar, i XML-format, för en viss begäran baserat på förfluten tid eller felsvarskoder.

#### <a name="web-server-diagnostics"></a>Diagnostik för webbserver
Du kan aktivera eller inaktivera följande typer av loggar:

-   Detaljerad felloggning - Detaljerad felinformation för HTTP-statuskoder som indikerar ett fel (statuskod 400 eller senare). Detta kan innehålla information som kan hjälpa till att avgöra varför servern returnerade felkoden.

-   Spårning av misslyckade begäranden - Detaljerad information om misslyckade begäranden, inklusive en spårning av IIS-komponenter som används för att bearbeta begäran och den tid det tar i varje komponent. Detta kan vara användbart om du försöker öka webbplatsens prestanda eller isolera vad som orsakar att ett visst HTTP-fel returneras.

-   Webbserverloggning - Information om HTTP-transaktioner med w3c-utökat loggfilformat. Detta är användbart när du fastställer övergripande platsmått, till exempel antalet hanterade begäranden eller hur många begäranden som kommer från en viss IP-adress.

#### <a name="application-diagnostics"></a>Programdiagnostik
[Med programdiagnostik](../../app-service/troubleshoot-diagnostic-logs.md) kan du samla in information som produceras av ett webbprogram. ASP.NET program kan använda klassen [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace) för att logga information till programdiagnostikloggen. I Programdiagnostik finns det två huvudtyper för händelser: de som är relaterade till programprestanda och de som är relaterade till programfel. Felen kan delas upp ytterligare i underavdelningar som anslutning, säkerhet och fel. Underavdelningen för fel är ofta relaterad till problem med programkoden.

I Programdiagnostik kan du gruppera händelser på följande vis:

-   Alla (visar alla händelser)
-   Programfel (visar undantagshändelser)
-   Prestanda (visar prestandahändelser)

## <a name="storage"></a>Storage
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure-lagringssäkerhet och sammanfattande information om dessa funktioner.

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Du kan skydda ditt lagringskonto med rollbaserad åtkomstkontroll (RBAC). Att begränsa åtkomsten baserat på [behovet](https://en.wikipedia.org/wiki/Need_to_know) av att känna till och [minst privilegier](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är absolut nödvändigt för organisationer som vill genomdriva säkerhetsprinciper för dataåtkomst. Dessa åtkomsträttigheter beviljas genom att tilldela lämplig RBAC-roll till grupper och program med ett visst tillämpningsområde. Du kan använda [inbyggda RBAC-roller](../../role-based-access-control/built-in-roles.md), till exempel Storage Account Contributor, för att tilldela privilegier till användare. Åtkomst till lagringsnycklarna för ett lagringskonto med [Azure Resource Manager-modellen](../../storage/blobs/security-recommendations.md) kan styras via Rollbaserad åtkomstkontroll (RBAC).

### <a name="shared-access-signature"></a>Signatur för delad åtkomst
En [signatur för delad åtkomst (Shared Access Signature, SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) ger delegerad åtkomst till resurser på ditt lagringskonto. SAS innebär att du kan bevilja en klient begränsad behörighet till objekt i ditt lagringskonto under en angiven period och med en angiven uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela dina kontoåtkomstnycklar.

### <a name="encryption-in-transit"></a>Kryptering vid överföring
Kryptering under överföring är en mekanism för att skydda data när den överförs över nätverk. Med Azure Storage kan du skydda data med:
-   [Kryptering på transportnivå](../../storage/blobs/security-recommendations.md), till exempel HTTPS när du överför data till eller från Azure Storage.

-   [Trådkryptering](../../storage/blobs/security-recommendations.md), till exempel [SMB 3.0-kryptering](../../storage/blobs/security-recommendations.md) för [Azure File-resurser](../../storage/files/storage-dotnet-how-to-use-files.md).

-   Kryptering på klientsidan, för att kryptera data innan de överförs till lagring och för att dekryptera data efter att de har överförts från lagring.

### <a name="encryption-at-rest"></a>Vilande kryptering
För många organisationer är datakryptering i vila ett obligatoriskt steg mot datasekretess, efterlevnad och datasuveränitet. Det finns tre säkerhetsfunktioner för Azure-lagring som tillhandahåller kryptering av data som är "i vila":

-   [Med storage service-kryptering](../../storage/common/storage-service-encryption.md) kan du begära att lagringstjänsten automatiskt krypterar data när du skriver den till Azure Storage.

-   [Kryptering på klientsidan](../../storage/common/storage-client-side-encryption.md) ger också funktionen kryptering i vila.

-   [Med Azure Disk Encryption](../azure-security-disk-encryption-overview.md) kan du kryptera os-diskar och datadiskar som används av en virtuell IaaS-dator.

### <a name="storage-analytics"></a>Lagringsanalys
[Azure Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) utför loggning och tillhandahåller måttdata för ett lagringskonto. Du kan använda dessa data för att spåra begäranden, analysera användningstrender och diagnostisera problem med ditt lagringskonto. Lagringsanalys loggar detaljerad information om lyckade och misslyckade begäranden till en lagringstjänst. Den här informationen kan användas för att övervaka enskilda begäranden och för att diagnostisera problem med en lagringstjänst. Förfrågningar loggas på bästa sätt. Följande typer av autentiserade begäranden loggas:
-   Lyckade begäranden.

-   Misslyckade begäranden, inklusive timeout, begränsning, nätverk, auktorisering och andra fel.

-   Begäranden med en SAS (Shared Access Signature), inklusive misslyckade och lyckade begäranden.

-   Begäranden om analysdata.

### <a name="enabling-browser-based-clients-using-cors"></a>Aktivera webbläsarbaserade klienter med CORS
[Cors (Cross-Origin Resource Sharing)](https://docs.microsoft.com/rest/api/storageservices/fileservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) är en mekanism som gör att domäner kan ge varandra behörighet för åtkomst till varandras resurser. Användaragenten skickar extra rubriker för att säkerställa att JavaScript-koden som läses in från en viss domän tillåts komma åt resurser som finns på en annan domän. Den senare domänen svarar sedan med extra rubriker som tillåter eller nekar den ursprungliga domänåtkomsten till dess resurser.

Azure Storage-tjänster stöder nu CORS så att när du har angett CORS-reglerna för tjänsten utvärderas en korrekt autentiserade begäran som görs mot tjänsten från en annan domän för att avgöra om den är tillåten enligt de regler du har angett.

## <a name="networking"></a>Nätverk
Avsnittet innehåller ytterligare information om viktiga funktioner i Azure-nätverkssäkerhet och sammanfattande information om dessa funktioner.

### <a name="network-layer-controls"></a>Kontroller för nätverkslager
Nätverksåtkomstkontroll är att begränsa anslutningen till och från specifika enheter eller undernät och representerar kärnan i nätverkssäkerheten. Målet med nätverksåtkomstkontrollen är att se till att dina virtuella datorer och tjänster endast är tillgängliga för användare och enheter som du vill att de ska vara tillgängliga för.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper
En [NSG (Network Security Group)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) är en grundläggande tillståndskänslig paketfiltreringsbrandvägg och gör att du kan styra åtkomsten baserat på en [5-tuppel](https://www.techopedia.com/definition/28190/5-tuple). NSG ger inte programlagerinspektion eller autentiserade åtkomstkontroller. De kan användas för att styra trafik som rör sig mellan undernät i ett Virtuellt Azure-nätverk och trafik mellan ett Virtuellt Azure-nätverk och Internet.

#### <a name="route-control-and-forced-tunneling"></a>Ruttkontroll och påtvingad tunnelning
Möjligheten att styra routningsbeteendet på dina Virtuella Azure-nätverk är en viktig nätverkssäkerhets- och åtkomstkontrollfunktion. Om du till exempel vill vara säker på att all trafik till och från ditt virtuella Azure-nätverk går igenom den virtuella säkerhetsinstallationen måste du kunna styra och anpassa routningsbeteendet. Du kan göra detta genom att konfigurera användardefinierade vägar i Azure.

[Med användardefinierade vägar](../../virtual-network/virtual-networks-udr-overview.md) kan du anpassa inkommande och utgående sökvägar för trafik som flyttar till och från enskilda virtuella datorer eller undernät för att försäkra den säkraste vägen som möjligt. [Tvingad tunnelning](https://www.petri.com/azure-forced-tunneling) är en mekanism som du kan använda för att säkerställa att dina tjänster inte tillåts initiera en anslutning till enheter på Internet.

Detta skiljer sig från att kunna acceptera inkommande anslutningar och sedan svara på dem. Front-end webbservrar måste svara på förfrågningar från Internet-värdar, så Internet-sourced trafik tillåts inkommande till dessa webbservrar och webbservrar kan svara.

Tvingad tunnelning används ofta för att tvinga utgående trafik till Internet för att gå igenom lokala säkerhets proxyservrar och brandväggar.

#### <a name="virtual-network-security-appliances"></a>Virtuella nätverkssäkerhetsenheter
Medan nätverkssäkerhetsgrupper, användardefinierade vägar och tvingande tunnelning ger dig en säkerhetsnivå i nätverks- och transportlagren i [OSI-modellen,](https://en.wikipedia.org/wiki/OSI_model)kan det finnas tillfällen då du vill aktivera säkerhet på högre nivåer i stacken. Du kan komma åt dessa förbättrade nätverkssäkerhetsfunktioner med hjälp av en azure-partnernätverkssäkerhetsinstallationslösning. Du kan hitta de senaste azure-partnernätverkssäkerhetslösningarna genom att besöka [Azure Marketplace](https://azure.microsoft.com/marketplace/) och söka efter "säkerhet" och "nätverkssäkerhet".

### <a name="azure-virtual-network"></a>Azure Virtual Network
Ett Azure-virtuellt nätverk (VNet) är en representation av ditt eget nätverk i molnet. Det är en logisk isolering av Azure-nätverksinfrastrukturen som är dedikerad till din prenumeration. Du kan helt styra IP-adressblocken, DNS-inställningarna, säkerhetsprinciperna och routingtabellerna inom det här nätverket. Du kan segmentera ditt virtuella nätverk i undernät och placera virtuella Azure IaaS-datorer (VMs) och/eller [Molntjänster (PaaS-rollinstanser)](../../cloud-services/cloud-services-choose-me.md) på Virtuella Azure-nätverk.

Du kan dessutom ansluta det virtuella nätverket till ditt lokala nätverk med ett av [anslutningsalternativen](../../vpn-gateway/index.yml) som finns i Azure. I princip kan du expandera ditt nätverk till Azure, med fullständig kontroll över IP-adressblock och de fördelar som Azure på företagsnivå erbjuder.

Azure-nätverk stöder olika scenarier för säker fjärråtkomst. Några av dessa inkluderar:

-   [Ansluta enskilda arbetsstationer till ett virtuellt Azure-nätverk](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

-   [Ansluta lokalt nätverk till ett virtuellt Azure-nätverk med ett VPN](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

-   [Ansluta lokalt nätverk till ett virtuellt Azure-nätverk med en dedikerad WAN-länk](../../expressroute/expressroute-introduction.md)

-   [Ansluta Virtuella Azure-nätverk till varandra](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

### <a name="vpn-gateway"></a>VPN Gateway
Om du vill skicka nätverkstrafik mellan ditt virtuella Azure-nätverk och din lokala webbplats måste du skapa en VPN-gateway för ditt virtuella Azure-nätverk. En [VPN-gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) är en typ av virtuell nätverksgateway som skickar krypterad trafik över en offentlig anslutning. Du kan också använda VPN-gateways för att skicka trafik mellan Virtuella Azure-nätverk via Azure-nätverksstrukturen.

### <a name="express-route"></a>Express Route
Microsoft Azure [ExpressRoute](../../expressroute/expressroute-introduction.md) är en dedikerad WAN-länk som låter dig utöka dina lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning som underlättas av en anslutningsleverantör.

![Express Route](./media/overview/azure-security-fig1.png)

Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning.

ExpressRoute-anslutningar går inte över det offentliga Internet och kan därför anses säkrare än VPN-baserade lösningar. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.


### <a name="application-gateway"></a>Application Gateway
Microsoft [Azure Application Gateway](../../application-gateway/overview.md) tillhandahåller en [ADC (Application Delivery Controller)](https://en.wikipedia.org/wiki/Application_delivery_controller) som en tjänst som erbjuder olika belastningsutjämningsfunktioner för lager 7 för ditt program.

![Application Gateway](./media/overview/azure-security-fig2.png)

Det gör att du kan optimera web farm produktivitet genom att avlasta CPU-intensiva TLS uppsägning till Application Gateway (även känd som "TLS avlastning" eller "TLS överbrygga"). Det ger också andra Layer 7 routningsfunktioner, inklusive round-robin-distribution av inkommande trafik, cookie-baserad sessionstillhörighet, URL-sökvägsbaserad routning och möjligheten att vara värd för flera webbplatser bakom en enda Application Gateway. Azure Application Gateway är en Layer 7-lastbalanserare.

Den tillhandahåller redundans och prestandabaserad routning av HTTP-begäranden mellan olika servrar, oavsett om de finns i molnet eller lokalt.

Ansökan ger många Application Delivery Controller (ADC) funktioner inklusive HTTP belastningsutjämning, cookie-baserad session tillhörighet, [TLS avlastning,](../../application-gateway/tutorial-restrict-web-traffic-powershell.md)anpassade hälsa sonder, stöd för flera plats, och många andra.

### <a name="web-application-firewall"></a>Brandvägg för webbaserade program
Brandvägg för webbprogram är en funktion i [Azure Application Gateway](../../application-gateway/overview.md) som skyddar webbprogram som använder ADC-funktioner (Application Delivery Control). Brandvägg för webbaserade program gör detta genom att skydda dem mot de flesta av de 10 vanligaste OWASP-säkerhetsproblemen.

![Brandvägg för webbaserade program](./media/overview/azure-security-fig1.png)

-   Skydd mot SQL-inmatning

-   Skydd mot vanliga webbattacker, som kommandoinmatning, dold HTTP-begäran, delning av HTTP-svar och attack genom införande av fjärrfil

-   Skydd mot åtgärder som inte följer HTTP-protokollet

-   Skydd mot avvikelser i HTTP-protokollet som att användaragent för värden och accept-huvud saknas

-   Skydd mot robotar, crawlers och skannrar

-   Identifiering av vanliga felkonfigurationer av programmet (det vill än Apache, IIS osv.)

En centraliserad brandvägg mot webbattacker underlättar säkerhetshanteringen och håller programmet bättre skyddat mot intrång. En brandväggslösning för webbaserade program kan även reagera snabbare på ett säkerhetshot genom att åtgärda en känd svaghet på en central plats jämfört med om korrigeringar ska utföras i varje enskilt webbprogram. Befintliga programgatewayer kan enkelt konverteras till en programgateway med brandvägg för webbprogram.

### <a name="traffic-manager"></a>Traffic Manager
Med Microsoft [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) kan du styra distributionen av användartrafik för tjänstslutpunkter i olika datacenter. Tjänstslutpunkter som stöds av Traffic Manager inkluderar virtuella Azure-datorer, webbappar och molntjänster. Du kan även använda Traffic Manager med externa slutpunkter som inte är Azure-relaterade. Traffic Manager använder DNS (Domain Name System) för att dirigera klientbegäranden till den lämpligaste slutpunkten baserat på en [trafikroutningsmetod](../../traffic-manager/traffic-manager-routing-methods.md) och slutpunkternas hälsotillstånd.

Traffic Manager tillhandahåller en rad trafikdirigeringsmetoder som passar olika programbehov, [hälsoövervakning för](../../traffic-manager/traffic-manager-monitoring.md)slutpunkter och automatisk redundans. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.

### <a name="azure-load-balancer"></a>Azure Load Balancer
[Azure Load Balancer](../../load-balancer/load-balancer-overview.md) levererar hög tillgänglighet och nätverksprestanda till dina program. Det är en layer 4-belastningsutjämnare (Layer 4), UDP) som distribuerar inkommande trafik mellan felfria instanser av tjänster som definierats i en belastningsbalanserad uppsättning. Azure Load Balancer kan konfigureras för att:

-   Belastningsutjämnad inkommande Internet-trafik till virtuella datorer. Den här konfigurationen kallas [Internet-vänd belastningsutjämning](../../load-balancer/concepts-limitations.md#publicloadbalancer).

-   Belastningsbalanstrafik mellan virtuella datorer i ett virtuellt nätverk, mellan virtuella datorer i molntjänster eller mellan lokala datorer och virtuella datorer i ett lokalt nätverk. Den här konfigurationen kallas [intern belastningsutjämning](../../load-balancer/concepts-limitations.md#internalloadbalancer).

- Vidarebefordra extern trafik till en viss virtuell dator

### <a name="internal-dns"></a>Intern DNS
Du kan hantera listan över DNS-servrar som används i ett virtuella nätverk i hanteringsportalen eller i nätverkskonfigurationsfilen. Kunden kan lägga till upp till 12 DNS-servrar för varje virtuella nätverk. När du anger DNS-servrar är det viktigt att du kontrollerar att du listar kundens DNS-servrar i rätt ordning för kundens miljö. DNS-serverlistor fungerar inte round-robin. De används i den ordning som de anges. Om den första DNS-servern i listan kan nås använder klienten den DNS-servern oavsett om DNS-servern fungerar korrekt eller inte. Om du vill ändra DNS-serverordern för kundens virtuella nätverk tar du bort DNS-servrarna från listan och lägger till dem i den ordning som kunden vill ha. DNS stöder tillgänglighetsaspekten av "CIA"-säkerhetstriaden.

### <a name="azure-dns"></a>Azure DNS
[Domännamnssystemet](https://technet.microsoft.com/library/bb629410.aspx), eller DNS, ansvarar för att översätta (eller lösa) en webbplats eller tjänstnamn till dess IP-adress. [Azure DNS](../../dns/dns-overview.md) är en värdtjänst för DNS-domäner som ger namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. DNS stöder tillgänglighetsaspekten av "CIA"-säkerhetstriaden.

### <a name="azure-monitor-logs-nsgs"></a>Azure Monitor loggar NSG:er
Du kan aktivera följande diagnostiska loggkategorier för NSG:

-   Händelse: Innehåller poster för vilka NSG-regler tillämpas på virtuella datorer och instansroller baserat på MAC-adress. Statusen för dessa regler samlas in var 60:e sekund.

-   Regelräknare: Innehåller poster för hur många gånger varje NSG-regel tillämpas för att neka eller tillåta trafik.

### <a name="security-center"></a>Security Center
[Azure Security Center](../../security-center/security-center-intro.md) analyserar kontinuerligt säkerhetstillståndet för dina Azure-resurser för bästa praxis för nätverkssäkerhet. När Security Center identifierar potentiella säkerhetsproblem skapas [rekommendationer](../../security-center/security-center-recommendations.md) som hjälper dig att konfigurera de kontroller som behövs för att härda och skydda dina resurser.

## <a name="compute"></a>Compute
Avsnittet innehåller ytterligare information om viktiga funktioner på det här området och sammanfattande information om dessa funktioner.

### <a name="antimalware--antivirus"></a>Antivirus & mot skadlig kod
Med Azure IaaS kan du använda programvara mot skadlig kod från säkerhetsleverantörer som Microsoft, Symantec, Trend Micro, McAfee och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot. [Microsoft Antimalware](antimalware.md) för Azure Cloud Services och Virtuella datorer är en skyddskapacitet som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Microsoft Antimalware ger konfigurerbara aviseringar när kända skadliga eller oönskade program försöker installera sig själv eller köras på dina Azure-system. Microsoft Antimalware kan också distribueras med Azure Security Center

### <a name="hardware-security-module"></a>Säkerhetsmodul för maskinvara
Kryptering och autentisering förbättrar inte säkerheten om inte själva nycklarna är skyddade. Du kan förenkla hanteringen och säkerheten för dina kritiska hemligheter och nycklar genom att lagra dem i [Azure Key Vault](../../key-vault/key-vault-overview.md). Key Vault ger möjlighet att lagra dina nycklar i maskinvarusäkerhetsmoduler (HSM) certifierade enligt FIPS 140-2 Nivå 2-standarder. Dina SQL Server-krypteringsnycklar för säkerhetskopiering eller [transparent datakryptering](https://msdn.microsoft.com/library/bb934049.aspx) kan alla lagras i Key Vault med nycklar eller hemligheter från dina program. Behörigheter och åtkomst till dessa skyddade objekt hanteras via [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

### <a name="virtual-machine-backup"></a>Säkerhetskopiering av virtuella datorer
[Azure Backup](../../backup/backup-overview.md) är en lösning som skyddar dina programdata med noll kapitalinvesteringar och minimala driftskostnader. Programfel kan skada dina data och mänskliga fel kan införa buggar i dina program som kan leda till säkerhetsproblem. Med Azure Backup är dina virtuella datorer som kör Windows och Linux skyddade.

### <a name="azure-site-recovery"></a>Azure Site Recovery
En viktig del av organisationens strategi [för affärskontinuitet/haveriberedskap (BCDR)](../../best-practices-availability-paired-regions.md) är att ta reda på hur du håller företagets arbetsbelastningar och appar igång när planerade och oplanerade avbrott inträffar. [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) hjälper till att dirigera replikering, redundans och återställning av arbetsbelastningar och appar så att de är tillgängliga från en sekundär plats om din primära plats går ner.

### <a name="sql-vm-tde"></a>SQL VM TDE
[Transparent datakryptering (TDE)](../../virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md) och kryptering på kolumnnivå (CLE) är SQL-serverkrypteringsfunktioner. Denna form av kryptering kräver kunder att hantera och lagra kryptografiska nycklar som du använder för kryptering.

Azure Key Vault (AKV) -tjänsten är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker och högtillgänglig plats. SQL Server Connector gör det möjligt för SQL Server att använda dessa nycklar från Azure Key Vault.

Om du kör SQL Server med lokala datorer finns det steg du kan följa för att komma åt Azure Key Vault från din lokala SQL Server-dator. Men för SQL Server i virtuella Azure-datorer kan du spara tid med hjälp av azure key vault-integreringsfunktionen. Med några Azure PowerShell-cmdletar för att aktivera den här funktionen kan du automatisera konfigurationen som krävs för en VIRTUELL SQL för att komma åt nyckelvalvet.

### <a name="vm-disk-encryption"></a>Kryptering av VM-disk
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) är en ny funktion som hjälper dig att kryptera dina virtuella diskar i Windows och Linux IaaS. Det gäller branschstandard BitLocker funktionen i Windows och DM-Crypt funktionen i Linux för att ge volymkryptering för OS och datadiskar. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera diskkrypteringsnycklar och hemligheter i din Key Vault-prenumeration. Lösningen säkerställer också att alla data på diskarna på den virtuella datorn krypteras i vila i din Azure-lagring.

### <a name="virtual-networking"></a>Virtuellt nätverk
Virtuella datorer behöver nätverksanslutning. För att stödja detta krav kräver Azure virtuella datorer som ska anslutas till ett Virtuellt Azure-nätverk. Ett Virtuellt Azure-nätverk är en logisk konstruktion som bygger på den fysiska Azure-nätverksstrukturen. Varje logiskt [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) är isolerat från alla andra Virtuella Azure-nätverk. Den här isoleringen säkerställer att nätverkstrafik i dina distributioner inte är tillgänglig för andra Microsoft Azure-kunder.

### <a name="patch-updates"></a>Korrigeringsuppdateringar
Korrigeringsuppdateringar utgör grunden för att hitta och åtgärda potentiella problem och förenkla processen för hantering av programuppdateringar, både genom att minska antalet programuppdateringar som du måste distribuera i företaget och genom att öka din förmåga att övervaka efterlevnaden.

### <a name="security-policy-management-and-reporting"></a>Hantering och rapportering av säkerhetspolicyer
[Security Center](../../security-center/security-center-intro.md) hjälper dig att förebygga, identifiera och svara på hot och ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Den ger integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och fungerar med ett brett ekosystem av säkerhetslösningar.

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
Skydda system, program och data börjar med identitetsbaserade åtkomstkontroller. De identitets- och åtkomsthanteringsfunktioner som är inbyggda i Microsofts affärsprodukter och -tjänster hjälper till att skydda din organisation och personliga information från obehörig åtkomst samtidigt som den görs tillgänglig för legitima användare när och var de behöver den.

### <a name="secure-identity"></a>Säker identitet
Microsoft använder flera säkerhetsrutiner och tekniker i sina produkter och tjänster för att hantera identitet och åtkomst.

-   [Multifaktorautentisering](https://azure.microsoft.com/services/multi-factor-authentication/) kräver att användare använder flera metoder för åtkomst, lokalt och i molnet. Det ger stark autentisering med en rad enkla verifieringsalternativ, samtidigt som användarna får en enkel inloggningsprocess.

-   [Microsoft Authenticator](https://aka.ms/authenticator) ger en användarvänlig multifaktorautentiseringsupplevelse som fungerar med både Microsoft Azure Active Directory- och Microsoft-konton och som innehåller stöd för bärbara och fingeravtrycksbaserade godkännanden.

-   [Lösenordsprincipövervakning](https://azure.microsoft.com/documentation/articles/active-directory-passwords-policy/) ökar säkerheten för traditionella lösenord genom att införa längd- och komplexitetskrav, tvingad periodisk rotation och kontoutelåsning efter misslyckade autentiseringsförsök.

-   [Tokenbaserad autentisering](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/) möjliggör autentisering via Azure Active Directory.

-   [Med rollbaserad åtkomstkontroll (RBAC)](https://azure.microsoft.com/documentation/articles/role-based-access-built-in-roles/) kan du bevilja åtkomst baserat på användarens tilldelade roll, vilket gör det enkelt att ge användarna endast den mängd åtkomst de behöver för att utföra sina arbetsuppgifter. Du kan anpassa RBAC enligt organisationens affärsmodell och risktolerans.

-   [Med integrerad identitetshantering (hybrididentitet)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/) kan du behålla kontrollen över användarnas åtkomst över interna datacenter och molnplattformar, vilket skapar en enda användaridentitet för autentisering och auktorisering till alla resurser.

### <a name="secure-apps-and-data"></a>Säkra appar och data
[Azure Active Directory](https://azure.microsoft.com/services/active-directory/), en omfattande molnlösning för identitets- och åtkomsthantering, hjälper till att skydda åtkomsten till data i program på plats och i molnet och förenklar hanteringen av användare och grupper. Den kombinerar grundläggande katalogtjänster, avancerad identitetsstyrning, säkerhet och hantering av programåtkomst och gör det enkelt för utvecklare att skapa principbaserad identitetshantering i sina appar. För att förbättra ditt Azure Active Directory kan du lägga till betalfunktioner med hjälp av Azure Active Directory Basic-, Premium P1- och Premium P2-versionerna.

| Gratis / Gemensamma funktioner     | Grundläggande funktioner    |Premium P1-funktioner |Premium P2-funktioner | Azure Active Directory Join – Endast relaterad windows 10-funktioner|
| :------------- | :------------- |:------------- |:------------- |:------------- |
|   [Katalogobjekt,](../../active-directory/active-directory-whatis.md) [Användar-/grupphantering (lägg till/uppdatera/ta bort)/ Användarbaserad etablering, Enhetsregistrering,](../../active-directory/active-directory-whatis.md) [Enkel inloggning (SSO)](../../active-directory/active-directory-whatis.md), [Lösenordsändring för självbetjäning för molnanvändare](../../active-directory/active-directory-whatis.md), [Anslut (synkroniseringsmotor som utökar lokala kataloger till Azure Active Directory)](../../active-directory/active-directory-whatis.md), [Säkerhets- och användningsrapporter](../../active-directory/active-directory-whatis.md)       |   [Gruppbaserad åtkomsthantering/etablering,](../../active-directory/active-directory-whatis.md) [självbetjäningslösenordsåterställning för molnanvändare](../../active-directory/active-directory-whatis.md), [Företagsbranding (inloggningssidor/anpassning av åtkomstpanelen)](../../active-directory/active-directory-whatis.md), [Programproxy](../../active-directory/active-directory-whatis.md), [SLA 99,9 %](../../active-directory/active-directory-whatis.md) |  [Självbetjäningsgrupp och apphantering/självbetjäningsprogramtillägg/dynamiska grupper,](../../active-directory/active-directory-whatis.md) [återställning/ändring/låsning/upplås/ lås upp självbetjäning med lokal tillbakaskrivning,](../../active-directory/active-directory-whatis.md) [multifaktorautentisering (moln- och lokal (MFA-server))](../../active-directory/active-directory-whatis.md), [MIM CAL + MIM Server](../../active-directory/active-directory-whatis.md), Cloud App [Discovery](../../active-directory/active-directory-whatis.md), [Connect Health](../../active-directory/active-directory-whatis.md), Automatisk [lösenordsvälvning för gruppkonton](../../active-directory/active-directory-whatis.md)|    [Identitetsskydd](../../active-directory/identity-protection/overview.md), [Privilegierad identitetshantering](../../active-directory/privileged-identity-management/pim-configure.md)|   [Gå med i en enhet till Azure AD, Desktop SSO, Microsoft Passport för Azure AD, AdministratörsbitLocker-återställning,](../../active-directory/active-directory-whatis.md) [AUTOMATISK MDM-registrering, BitLocker-återställning med självbetjäning, Ytterligare lokala administratörer till Windows 10-enheter via Azure AD Join](../../active-directory/active-directory-whatis.md)|


- [Cloud App Discovery](../../active-directory/cloudappdiscovery-get-started.md) är en premiumfunktion i Azure Active Directory som gör att du kan identifiera molnprogram som används av de anställda i din organisation.

- [Azure Active Directory Identity Protection](https://azure.microsoft.com/documentation/articles/active-directory-identityprotection/) är en säkerhetstjänst som använder Azure Active Directory anomaliidentifieringsfunktioner för att tillhandahålla en konsoliderad vy över riskidentifieringar och potentiella säkerhetsproblem som kan påverka organisationens identiteter.

- [Med Azure Active Directory Domain Services](https://azure.microsoft.com/services/active-directory-ds/) kan du ansluta till virtuella Azure-datorer till en domän utan att behöva distribuera domänkontrollanter. Användare loggar in på dessa virtuella datorer med hjälp av sina active directory-autentiseringsuppgifter och kan komma åt resurser sömlöst.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) är en mycket tillgänglig, global identitetshanteringstjänst för konsumentinriktade appar som kan skalas till hundratals miljoner identiteter och integreras på mobila plattformar och webbplattformar. Dina kunder kan logga in på alla dina appar via anpassningsbara upplevelser som använder befintliga konton i sociala medier, eller så kan du skapa nya fristående autentiseringsuppgifter.

- [Azure Active Directory B2B Collaboration](https://aka.ms/aad-b2b-collaboration) är en säker partnerintegrationslösning som stöder dina relationer mellan företag genom att göra det möjligt för partner att komma åt dina företagsprogram och data selektivt med hjälp av deras självhanterade identiteter.

- [Med Azure Active Directory Join](https://azure.microsoft.com/documentation/articles/active-directory-azureadjoin-overview/) kan du utöka molnfunktionerna till Windows 10-enheter för centraliserad hantering. Det gör det möjligt för användare att ansluta till företags- eller organisationsmolnet via Azure Active Directory och förenklar åtkomsten till appar och resurser.

- [Azure Active Directory Application Proxy](https://azure.microsoft.com/documentation/articles/active-directory-application-proxy-get-started/) ger SSO och säker fjärråtkomst för webbprogram som finns lokalt.

## <a name="next-steps"></a>Efterföljande moment

- Förstå ditt [delade ansvar i molnet](shared-responsibility.md).

- Lär dig mer om hur [Azure Security Center](https://azure.microsoft.com/services/security-center/) kan hjälpa dig att förebygga, identifiera och svara på hot med ökad synlighet och kontroll över säkerheten för dina Azure-resurser.
