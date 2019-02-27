---
title: Vad är Azure Analysis Services? | Microsoft Docs
description: Få en helhetsbild av Analysis Services i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: overview
ms.date: 02/14/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 114dac10592213d3b2fdfff909913039b3dba344
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429442"
---
# <a name="what-is-azure-analysis-services"></a>Vad är Azure Analysis Services?

![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services är en fullständigt hanterad plattform som tjänst (PaaS) som tillhandahåller datamodeller i företagsklass i molnet. Använd avancerade kombinations- och modelleringsfunktioner för att kombinera data från flera datakällor, definiera mätvärden och skydda dina data i en enda tillförlitlig tabellsemantisk datamodell. Datamodellen ger ett enklare och snabbare sätt för användare att bläddra igenom stora mängder data för ad hoc-dataanalys.

![Datakällor](./media/analysis-services-overview/aas-overview-overall.png)

**Video:** Mer information om hur Azure Analysis Services passar in med Microsofts övergripande BI-funktioner finns i [Översikt över Azure Analysis Services](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4).

## <a name="get-up-and-running-quickly"></a>Kom igång snabbt

På Azure Portal kan du [skapa en server](analysis-services-create-server.md) på några minuter. Och du kan skapa servrar med hjälp av en deklarativ mall med Azure Resource Manager-[mallar](../azure-resource-manager/resource-manager-create-first-template.md) och PowerShell. Du kan distribuera serverresurser tillsammans med andra Azure-komponenter såsom lagringskonton och Azure Functions, med en enda mall. 

**Video:** Mer information om hur du kan använda Azure Automation för att snabbare skapa servrar finns i [Automatiseringsdistribution](https://channel9.msdn.com/series/Azure-Analysis-Services/AzureAnalysisServicesAutomation).

Azure Analysis Services integreras med många Azure-tjänster vilket gör att du kan skapa lösningar för avancerade analyser. Integreringen med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) ger dig säker, rollbaserad åtkomst till dina viktiga data. Integrera i en [Azure Data Factory](../data-factory/introduction.md)-pipeline genom att omfatta en aktivitet som läser in data i modellen. [Azure Automation](../automation/automation-intro.md) och [Azure Functions](../azure-functions/functions-overview.md) kan användas för att utföra enkel orkestrering av modeller med anpassad kod. 

## <a name="the-right-tier-when-you-need-it"></a>Rätt nivå när du behöver den

Azure Analysis Services är tillgängligt på nivåerna **Developer**, **Basic** och **Standard**. Inom varje nivå varierar kostnaderna för prenumerationsavtalen utifrån bearbetningskraft, QPU:er och minnesstorlek. När du skapar en server kan välja du en plan inom en nivå. Du kan ändra planer uppåt eller nedåt i samma nivå eller uppgradera till en högre nivå, men det går inte att nedgradera från en högre nivå till en lägre nivå.

### <a name="developer-tier"></a>Developer-nivå

Den här nivån rekommenderas för utvärderings-, utvecklings- och testscenarier. En enskild plan inkluderar samma funktioner som på Standard-nivån, men funktionerna för processorkraft, QPU:er och minnesstorlek är begränsade. Skalning av frågerepliker *är inte tillgängligt* på den här nivån. Den här nivån omfattas inte av något serviceavtal.

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


### <a name="basic-tier"></a>Basic-nivå

Nivån rekommenderas för produktionslösningar med mindre tabellmodeller, begränsad användarsamtidighet och enkla krav för datauppdatering. Skalning av frågerepliker *är inte tillgängligt* på den här nivån. Perspektiv, flera partitioner och DirectQuery-funktioner för tabellmodeller *stöds inte* på den här nivån.  

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

### <a name="standard-tier"></a>Standard-nivå

Den här nivån passar bäst för verksamhetskritiska produktionsprogram som kräver elastisk användarsamtidighet och har snabbväxande datamodeller. Den har stöd för avancerad datauppdatering för datamodellsuppdateringar i nära realtid och stöder alla funktioner för tabellmodeller.

|Planera  |QPU:er  |Minne (GB)  |
|---------|---------|---------|
|S0    |    40     |    10     |
|S1    |    100     |    25     |
|S2    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Inte tillgänglig i alla regioner.  

## <a name="availability-by-region"></a>Tillgänglighet efter region

Azure Analysis Services stöds i regioner över hela världen. Planer som stöds och tillgänglighet för frågereplik är beroende av den region som väljs. Tillgänglighet för plan och frågereplik kan ändras beroende på behov och tillgängliga resurser för varje region. 

### <a name="americas"></a>Nord- och Sydamerika

|Region  | Planer som stöds | Frågerepliker (endast standardplaner) |
|---------|---------|:---------:|
|Södra Brasilien     |    B1, B2, S0, S1, S2, S4, D1     |     1    |
|Centrala Kanada    |     B1, B2, S0, S1, S2, S4, D1    |     1    |
|Östra USA     |     B1, B2, S0, S1, S2, S4, D1    |    1     |
|USA, östra 2     |     B1, B2, S0, S1, S2, S4, D1   |    7    |
|USA, östra 2     |     S8, S9   |    1    |
|Norra centrala USA     |     B1, B2, S0, S1, S2, S4, D1     |    1     |
|Centrala USA     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|Södra centrala USA     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|Västra centrala USA   |     B1, B2, S0, S1, S2, S4, D1    |    3     |
|Västra USA     |    B1, B2, S0, S1, S2, S4, D1    |    7   |
|Västra USA     |    S8, S9   |    2  |
|USA, västra 2    |    B1, B2, S0, S1, S2, S4, D1    |    3   |
|USA, västra 2    |    S8, S9  |    1     |

### <a name="europe"></a>Europa

|Region  | Planer som stöds | Frågerepliker (endast standardplaner) |
|---------|---------|:---------:|
|Norra Europa     |    B1, B2, S0, S1, S2, S4, D1      |    7     |
|Storbritannien, södra     |    B1, B2, S0, S1, S2, S4, D1      |     1    |
|Västra Europa     |    B1, B2, S0, S1, S2, S4, D1   |    7    |
|Västra Europa    |   S8, S9  |  1  |

### <a name="asia-pacific"></a>Asien och stillahavsområdet 

|Region  | Planer som stöds | Frågerepliker (endast standardplaner) |
|---------|---------|:---------:|
|Östra Australien     |    B1, B2, S0, S1, S2, S4, D1     |    3     |
|Östra Australien     |    S8, S9    |    1     |
|Sydöstra Australien     | B1, B2, S0, S1, S2, S4, D1       |    1     |
|Östra Japan     |   B1, B2, S0, S1, S2, S4, D1       |    1     |
|Sydostasien     |     B1, B2, S0, S1, S2, S4, S8, S9, D1     |   1      |
|Indien, västra     |    B1, B2, S0, S1, S2, S4, D1     |    1     |

## <a name="scale-to-your-needs"></a>Skala efter behov

### <a name="scale-updown-pause-and-resume"></a>Skala upp\ner, pausa och återuppta

Gå upp, ned eller pausa din server. Använd Azure Portal eller få fullständig kontroll direkt med hjälp av PowerShell. Betala endast för det du använder.  

### <a name="scale-out-resources-for-fast-query-responses"></a>Skala ut resurser för snabba frågesvar

Med skalning distribueras klientfrågor över flera *frågerepliker* i en frågepool. Frågerepliker har synkroniserade kopior av dina tabellmodeller. Genom att sprida frågearbetsbelastningen kan svarstiderna minskas under stora frågearbetsbelastningar. Modellbearbetning kan separeras från frågepoolen. Det här säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. 

Du kan skapa en frågepool med upp till sju ytterligare frågerepliker (åtta totalt, inklusive din server). Antalet frågerepliker som du kan ha i din pool beror på den valda planen och regionen. Frågerepliker kan inte spridas utanför din serverregion. Frågerepliker debiteras med samma avgift som din server.

Du kan skala ut frågerepliker efter dina behov, precis som för att ändra nivån. Konfigurera skalning i portalen eller med hjälp av REST API: er. Läs mer i [Skalning av Azure Analysis Services](analysis-services-scale-out.md).

## <a name="pricing"></a>Prissättning

Totalkostnad beror på ett antal faktorer, exempelvis din valda region, nivå, frågerepliker och paus/återuppta. Använd [Azure Analysis Services pris](https://azure.microsoft.com/pricing/details/analysis-services/)-kalkylator för att fastställa vanlig prissättning för din region. Det här verktyget beräknar prissättning för en enskild serverinstans för en enskild region. Tänk på att frågerepliker debiteras med samma avgift som servern. 

## <a name="built-on-sql-server-analysis-services"></a>Bygger på SQL Server Analysis Services

Azure Analysis Services är kompatibelt med många fantastiska funktioner som redan finns i SQL Server Analysis Services Enterprise. Azure Analysis Services stöder tabellmodeller på [kompatibilitetsnivå](analysis-services-compat-level.md) 1200 eller högre. Tabellmodeller är relationsbaserade modelleringskonstruktioner (modeller, tabeller, kolumner), som uttrycks i objektdefinitioner för tabellmetadata i TMSL-kod (Tabular Model Scripting Language) och TOM-kod (Tabular Object Model). Partitioner, perspektiv, säkerhet på radnivå, dubbelriktade relationer och översättningar stöds*. Flerdimensionella modeller och PowerPivot för SharePoint stöds *inte* i Azure Analysis Services.

Tabellmodeller både i minnet och DirectQuery-lägena stöds. Tabellmodeller i InMemory-läge (standard) har stöd för flera datakällor. Eftersom modelldata är mycket komprimerat och cachelagrade i minnet ger det här läget snabbast frågesvar över stora mängder data. Det ger också störst flexibilitet för komplexa datauppsättningar och frågor. Partitionering möjliggör stegvis inläsning, ökar parallellisering och minskar minnesanvändningen. Andra avancerade funktioner för datamodeller såsom beräknade tabeller samt alla DAX-funktioner stöds. InMemory-modeller måste uppdateras (bearbetas) för att uppdatera cachelagrade data från datakällor. Tack vare Azures stöd för tjänsthuvudnamn får du en flexibel oövervakad uppdatering med PowerShell, TOM, TMSL och REST, vilket innebär att du alltid kan vara säker på att dina modelldata är aktuella. 

DirectQuery-läget* använder serverdelens relationsdatabas för lagring och frågekörning. Extremt stora datauppsättningar i en enskild SQL Server, SQL Server Data Warehouse, Azure SQL Database, Azure SQL Data Warehouse, Oracle och Teradata-datakällor stöds. Serverdelens datauppsättningar kan överskrida tillgängligt resursminne för servern. Det behövs inga avancerade uppdateringsscenarier för datamodeller. Det finns vissa begränsningar, till exempel begränsade datakälltyper, DAX-formelbegränsningar, och vissa avancerade funktioner för datamodeller stöds inte. Se [Direkt frågeläge](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular) innan du bestämmer det bästa läget för dig.

\* Funktionstillgänglighet beror på nivån.

## <a name="supported-data-sources"></a>Datakällor som stöds

Tabellmodeller i Azure Analysis Services stöder en mängd olika datakällor från enkla textfiler till stordata i Azure Data Lake Store. Läs mer i avsnittet [Datakällor som stöds i Azure Analysis Services](analysis-services-datasource.md).

## <a name="your-data-is-secure"></a>Dina data är skyddade

Azure Analysis Services tillhandahåller säkerhet för känsliga data på flera nivåer. Som en Azure-tjänst innehåller Analysis Services den **grundläggande** nivån för distribuerade överbelastningsattacker, vilken aktiveras automatiskt som en del av Azure-plattformen. Mer information finns i [Översikt över Azure DDoS Protection Standard](../virtual-network/ddos-protection-overview.md). 

På servernivå har Analysis Services en brandvägg, Azure-autentisering, serveradministratörsroller och kryptering för serversidan. På datamodellnivå säkerställer användarroller, säkerhet på radnivå och säkerhet på objektnivå att dina data är säkra och endast ses av de användare som är avsedda att se den.

### <a name="firewall"></a>Brandvägg

Azure Analysis Services brandvägg blockerar alla klientanslutningar förutom IP-adresserna som anges i regler. Som standard aktiveras inte brandväggsskydd för nya servrar. Vi rekommenderar att brandväggsskyddet är aktiverat och att regler konfigureras som en del av serverns etableringsskript eller i portalen omedelbart efter att servern har skapats. Konfigurera regler för tillåtna IP-adresser av enskilda klient-IP-adresser eller av intervaller. Power BI (service)-anslutningar kan också tillåtas eller blockeras. Konfigurera brandväggen och regler i portalen eller med hjälp av PowerShell. Läs mer i [Konfigurera en serverbrandvägg](analysis-services-qs-firewall.md).

### <a name="authentication"></a>Autentisering

Användarautentisering hanteras av [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md). Vid inloggning använder användare en organisationskontoidentitet med rollbaserad åtkomst till databasen. Användaridentiteter måste vara medlemmar i standard-Azure Active Directory för den prenumeration som servern finns i. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

### <a name="data-security"></a>Datasäkerhet

Azure Analysis Services använder Azure Blob Storage för lagring och metadata för Analysis Services-databaser. Datafiler i Blob krypteras med [Azure Blob Server Side Encryption (SSE)](../storage/common/storage-service-encryption.md). När du använder läget Direct Query lagras endast metadata. Faktiska data hämtas via krypterade protokoll från datakällan när frågan körs.

Du kan skydda åtkomsten till datakällor lokalt inom din organisation genom att installera och konfigurera en [Lokal datagateway](analysis-services-gateway.md). Gateways ger åtkomst till data för både DirectQuery- och InMemory-läget.

### <a name="roles"></a>Roller

Analysis Services använder [rollbaserad auktorisering](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular) som ger åtkomst till servern och åtgärder, objekt och data för modelldatabaser. Alla användare som har åtkomst till en server eller databas gör detta med sina Azure AD-användarkonto i en tilldelad roll. Serveradministratörsrollen är på serverresursnivå. Som standard ingår det konto som används när du skapar en server automatiskt i administratörsrollen för servern. Ytterligare användar- och gruppkonton har lagts till med hjälp av portalen, SSMS eller PowerShell.
  
Icke-administrativa slutanvändare som frågar efter data beviljas åtkomst genom databasroller. En databasroll skapas som ett separat objekt i databasen och gäller endast för databasen som rollen har skapats i. Databasroller definieras av (databasens) administratörs-, läs- och läs- och processbehörigheter. Användar- och gruppkonton läggs till med hjälp av SSMS eller PowerShell.

### <a name="row-level-security"></a>Säkerhet på radnivå

Tabellmodeller på alla kompatibilitetsnivåer har stöd för säkerhet på radnivå. Säkerhet på radnivå konfigureras i modellen med hjälp av DAX-uttryck som definierar raderna i en tabell och alla rader i många riktningar i en relaterad tabell som en användare kan fråga. Radfilter som använder DAX-uttryck definieras för läs- och läs- och processbehörigheter. 

### <a name="object-level-security"></a>Säkerhet på objektnivå 

Tabellmodeller på kompatibilitetsnivån 1400 stöder säkerhet på objektnivå, vilket inkluderar säkerhet på tabellnivå och säkerhet på kolumnnivå. Säkerhet på objektnivå anges i JSON-baserad metadata i Model.bim-filen med hjälp av TMSL eller TOM. Läs mer i [Säkerhet på objektnivå](https://docs.microsoft.com/sql/analysis-services/tabular-models/object-level-security).

### <a name="automation-through-service-principals"></a>Automation via tjänsthuvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. Tjänstens huvudnamn används med Azure Automation, obevakat läge i PowerShell, anpassade klientprogram och webbprogram för att automatisera vanliga uppgifter som datauppdatering, skala upp/ned och pausa/återuppta. Behörigheter tilldelas till tjänstens huvudnamn via rollmedlemskap. Läs mer i [Automatisering med tjänstens huvudnamn](analysis-services-service-principal.md).

### <a name="azure-governance"></a>Azure-styrning

Azure Analysis Services lyder under [villkoren för Microsoft Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) och [Microsofts sekretesspolicy](https://privacy.microsoft.com/privacystatement).
Mer information om säkerhet i Azure finns på [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

## <a name="use-the-tools-you-already-know"></a>Använd de verktyg som du är van vid

![BI-utvecklarverktyg](./media/analysis-services-overview/aas-overview-dev-tools.png)

### <a name="visual-studio"></a>Visual Studio

Utveckla och distribuera modeller med kostnadsfria [SQL Server Data Tools (SSDT) för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT innehåller Analysis Services-projektmallar som du kommer igång med snabbare. SSDT innehåller nu den moderna Get Data-datakällfrågan och kombinationsfunktioner för 1400-tabellmodeller. Om du är bekant med Hämta data i Power BI Desktop och Excel 2016 vet du redan hur lätt det är att skapa välanpassade datakällfrågor. 

Om du använder Visual Studio 2017 är Microsoft Analysis Services-projekt tillgängliga som ett kostnadsfritt installerbart VSIX-paket. [Ladda ned från Marketplace](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects).

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Hantera dina servrar och modelldatabaser med hjälp av [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). Ansluta till servrar i molnet. Kör TMSL-skript direkt från XMLA-frågefönstret och automatisera uppgifter med hjälp av TMSL-skript och PowerShell. Nya funktioner och funktionaliteter införs snabbt och SSMS uppdateras varje månad.

### <a name="powershell"></a>PowerShell

Under hanteringsaktiviteter för serverresurser såsom att skapa serverresurser, pausa eller återuppta serveråtgärder eller ändra servicenivån, används cmdletar för Azure Resource Manager (AzureRM). Under andra aktiviteter för hantering av databaser, till exempel att lägga till eller ta bort rollmedlemmar, behandla eller köra TMSL-skript, används cmdletar i SqlServer-modulen. Se [Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md) för mer information.

### <a name="object-model-and-scripting"></a>Objektmodell och skript

Tabellmodeller ger snabb utveckling och är mycket anpassningsbara. Tabellmodeller innehåller en [TOM](https://docs.microsoft.com/sql/analysis-services/tabular-model-programming-compatibility-level-1200/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (Tabular Object Model) som används för att beskriva modellobjekt. TOM exponeras i JSON via [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) och datadefinitionsspråket AMO via namnområdet [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx). 

## <a name="supports-the-latest-client-tools"></a>Stöder de senaste klientverktygen

![Datavisualiseringar](./media/analysis-services-overview/aas-overview-clients.png)

Moderna datautforsknings- och visualiseringsverktyg som Power BI, Excel, Reporting Services och verktyg från tredje part stöds och ger användarna interaktiva och högvisuella insikter om dina modelldata. 

## <a name="monitoring-and-diagnostics"></a>Övervakning och diagnostik

Azure Analysis Services är integrerat med Azure-mått, vilket ger ett omfattande antal resursspecifika mått som hjälper dig att övervaka prestanda och hälsotillståndet för dina servrar. Läs [Övervaka servermått](analysis-services-monitor.md) för mer information. Registrera mått med [Azure resursdiagnostikloggar](../azure-monitor/platform/diagnostic-logs-overview.md). Övervaka och skicka loggar till [Azure Storage](https://azure.microsoft.com/services/storage/), strömma dem till [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) och exportera dem till [Azure Monitor-loggar](https://azure.microsoft.com/services/log-analytics/), en tjänst från [Azure](https://www.microsoft.com/cloud-platform/operations-management-suite). Läs mer i [Konfigurera diagnostisk loggning](analysis-services-logging.md).

Azure Analysis Services stöder även [dynamiska hanteringsvyer (DMV: er)](https://docs.microsoft.com/sql/analysis-services/instances/use-dynamic-management-views-dmvs-to-monitor-analysis-services). DMV:s gränssnitt baseras på SQL-syntax och har en schemaraduppsättning som returnerar metadata samt övervakar information om serverinstanser.

## <a name="documentation"></a>Dokumentation

Dokumentation specifik för Azure Analysis Services ingår. Använd innehållsförteckningen på vänster sida av webbläsarskärmen för att hitta artiklar. 

Eftersom Azure Analysis Services tabellmodeller är ungefär som tabellmodeller i SQL Server Analysis Services så finns ett omfattande bibliotek med delade artiklar om begrepp, procedurer, utvecklare och referenser i [Dokumentationen för SQL Server Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services). Artiklar i dokumentation för SQL Server Analysis Services visar om de gäller även för Azure Analysis Services med banderollen GÄLLER FÖR under rubriken.

![Delad dokumentation](./media/analysis-services-overview/aas-overview-applies-to.png)

### <a name="contribute"></a>Bidra!

Analysis Services-dokumentation, som den här artikeln, är en öppen källa. Om du har ett GitHub-konto kan du redigera en artikel genom att klicka på Redigera (pennan) i det övre högra hörnet av webbläsarskärmen. Använd redigeringsprogrammet i webbläsaren och klicka på Föreslå filändring. 

![Delad dokumentation](./media/analysis-services-overview/aas-overview-edit.png)

Ditt bidrag granskas av dokumentationsteamet och om det godkänns visas ditt GitHub-kontonamn som deltagare. Mer information finns i [guide för dokumentdeltagare](https://docs.microsoft.com/contribute/).

Dokumentation för Azure Analysis Services använder också [GitHub-problem](https://docs.microsoft.com/teamblog/a-new-feedback-system-is-coming-to-docs). Du kan lämna feedback om produkten eller dokumentationen. Använd **Feedback** längst ned i en artikel. GitHub-problem har ännu inte aktiverats för dokumentation för SQL Server Analysis Services. 

## <a name="blogs"></a>Bloggar

Saker och ting ändras snabbt. Få den senaste informationen i [Analysis Services-teambloggen](https://blogs.msdn.microsoft.com/analysisservices/) och i [Azure-bloggen](https://azure.microsoft.com/blog/).

## <a name="community"></a>Community

Analysis Services har ett levande användarforum. Delta i konversationen i [Azure Analysis Services-forumet](https://aka.ms/azureanalysisservicesforum).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Registrera dig för en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/offers/ms-azr-0044p/)   

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en server – portalen](analysis-services-create-server.md)   

> [!div class="nextstepaction"]
> [Snabbstart: Skapa en server – PowerShell](analysis-services-create-powershell.md)  
