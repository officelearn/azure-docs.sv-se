---
title: "Vad är Azure Analysis Services? | Microsoft Docs"
description: "Få en helhetsbild av Analysis Services i Azure."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 368589509b163cacf495fd0be893a8953fe2066e
ms.openlocfilehash: ff03ce6463176bad8e8d89985441d23c7d76b183
ms.contentlocale: sv-se
ms.lasthandoff: 08/17/2017

---
# <a name="what-is-azure-analysis-services"></a>Vad är Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services innehåller modeller med företagsklassdata i molnet. Det här är en fullständigt hanterad plattform som en tjänst (PaaS), integrerad med Azure-dataplattformstjänsterna. 

Med Analysis Services kan du blanda och kombinera data från flera källor, definiera mätvärden och skydda dina data i en enda tillförlitlig semantisk datamodell. Datamodellen gör det enkelt för användarna att snabbt söka igenom stora mängder data med klientprogram som Power BI, Excel, Reporting Services, program från tredjepart och anpassade appar.

![Datakällor](./media/analysis-services-overview/aas-overview-data-sources.png)

Titta på [den här videon](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) som visar hur Azure Analysis Services passar in bland Microsofts BI-funktioner och fördelarna med att ha dina datamodeller i molnet.

## <a name="built-on-sql-server-analysis-services"></a>Bygger på SQL Server Analysis Services
Azure Analysis Services är kompatibelt med många fantastiska funktioner som redan finns i SQL Server Analysis Services Enterprise. Azure Analysis Services fungerar med tabellmodeller på [kompatibilitetsnivå](analysis-services-compat-level.md) 1 200 och 1 400. Partitioner, säkerhet på radnivå, dubbelriktade relationer och översättningar stöds. Minnes- och DirectQuery-lägena innebär att du blixtsnabbt kan skicka frågor via omfattande och komplexa datauppsättningar.

Tabellmodeller ger snabb utveckling och är mycket anpassningsbara. För utvecklare innehåller tabellmodeller en TOM (Tabular Object Model) som används för att beskriva modellobjekt. TOM exponeras i JSON via [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) och datadefinitionsspråket AMO via namnområdet [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

## <a name="better-with-azure"></a>Bättre med Azure
Azure Analysis Services integreras med många Azure-tjänster vilket gör att du kan skapa lösningar för avancerade analyser. Integreringen med [Azure Active Directory](../active-directory/active-directory-whatis.md) ger dig säker, rollbaserad åtkomst till dina viktiga data. Integrera i en [Azure Data Factory](../data-factory/data-factory-introduction.md)-pipeline genom att omfatta en aktivitet som läser in data i modellen. [Azure Automation](../automation/automation-intro.md) och [Azure Functions](../azure-functions/functions-overview.md) kan användas för att utföra enkel orkestrering av modeller med anpassad kod.

## <a name="get-up-and-running-quickly"></a>Kom igång snabbt
På Azure Portal kan du [skapa en server](analysis-services-create-server.md) på några minuter. Och du kan etablera servrar med hjälp av en deklarativ mall med Azure Resource Manager-[mallar](../azure-resource-manager/resource-manager-create-first-template.md) och PowerShell. Du kan distribuera flera tjänster tillsammans med andra Azure-komponenter, till exempel lagringskonton och Azure Functions, med en enda mall. 

När du har skapat en server kan skapa du en tabellmodell direkt i Azure Portal. Med den nya [webbdesignerfunktionen](analysis-services-create-model-portal.md) (förhandsversion) kan du ansluta till en Azure SQL Database, Azure SQL Data Warehouse-datakälla eller importera en Power BI Desktop PBIX-fil. Relationer mellan tabeller skapas automatiskt, och du kan skapa mått eller redigera filen model.bim i json-format direkt från webbläsaren.

## <a name="scale-to-your-needs"></a>Skala efter behov
Azure Analysis Services är tillgängligt på nivåerna Developer, Basic och Standard. Inom varje nivå varierar kostnaderna för prenumerationsavtalen utifrån bearbetningskraft, QPU:er och minnesstorlek. När du skapar en server kan välja du en plan inom en nivå. Du kan ändra planer uppåt eller nedåt i samma nivå eller uppgradera till en högre nivå, men det går inte att nedgradera från en högre nivå till en lägre nivå.

Skala upp, ned eller pausa din server. Använd Azure Portal eller få fullständig kontroll direkt med hjälp av PowerShell. Betala endast för det du använder. Mer information om de olika planerna och nivåerna och en prisnivåkalkylator som kan användas för att välja rätt plan, finns i [prisinformationen för Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="keep-your-data-close"></a>Förvara dina data nära
Azure Analysis Services-servrar kan skapas i följande [Azure-regioner](https://azure.microsoft.com/regions/):

| Nord- och Sydamerika | Europa | Asien och stillahavsområdet |
|----------|--------|--------------|
|  Södra Brasilien<br> Centrala Kanada<br> Östra USA 2<br> Norra centrala USA<br> Södra centrala USA<br> Västra centrala USA<br> Västra USA | Norra Europa<br> Storbritannien, södra<br> Västra Europa |   Sydöstra Australien<br> Östra Japan<br> Sydostasien<br> Indien, västra  |

Nya områden läggs till hela tiden, så den här listan kan vara ofullständig. Du kan välja en plats när du skapar en server i Azure Portal eller med hjälp av Azure Resource Manager-mallar. Välj en plats närmast din största användarbas för att få bästa möjliga prestanda. Garantera [hög tillgänglighet](analysis-services-bcdr.md) genom att distribuera modellerna på redundanta servrar i flera områden.

## <a name="migrate-your-existing-tabular-models"></a>Migrera dina befintliga tabellmodeller
Om du redan har befintliga lokala SQL Server Analysis Services-modellösningar kan du migrera till Azure Analysis Services utan betydande förändringar. Du kan använda SSDT för att distribuera din modell till servern vid migrering. I SSMS kan du även använda säkerhetskopiering och återställning eller TMSL.

Om du har lokala datakällor måste du installera och konfigurera en [lokal datagateway](analysis-services-gateway.md). Om du har roller och rollmedlemmar som redan har konfigurerats migreras rollerna, men du behöver läsa rollmedlemmarna med hjälp av SSMS eller PowerShell.

## <a name="connect-to-popular-data-sources"></a>Anslut till populära datakällor
Azure Analysis Services har stöd för [att ansluta till datakällor](analysis-services-datasource.md) lokalt i din organisation eller i molnet. Du kan kombinera data från datakällor som finns både lokalt och i molnet. På så sätt får du en hybridlösning. 

Nya 1400-tabellmodeller använder den moderna Get Data-funktionen i SSDT, baserat på M-formelfrågespråket. Med Get Data får du tillgång till fler datatransformerings- och kombinationsprogramfunktioner och möjlighet att skapa och redigera egna avancerade M-formelspråkfrågor. Med 1400-tabellmodeller kan du till exempel modellera på datafiler i Azure Blob Storage.

## <a name="use-the-tools-you-already-know"></a>Använd de verktyg som du är van vid

![BI-utvecklarverktyg](./media/analysis-services-overview/aas-overview-dev-tools.png)

#### <a name="sql-server-data-tools-ssdt-for-visual-studio"></a>SQL Server Data Tools (SSDT) för Visual Studio
Utveckla och distribuera modeller med kostnadsfria [SQL Server Data Tools (SSDT) för Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT innehåller Analysis Services-projektmallar som du kommer igång med snabbare. SSDT innehåller nu den moderna Get Data-datakällfrågan och kombinationsfunktioner för 1400-tabellmodeller. Om du är bekant med Hämta data i Power BI Desktop och Excel 2016 vet du redan hur lätt det är att skapa välanpassade datakällfrågor.

#### <a name="sql-server-management-studio"></a>SQL Server Management Studio
Hantera dina servrar och modelldatabaser med hjälp av [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Ansluta till servrar i molnet. Kör TMSL skript direkt från XMLA-frågefönstret och automatisera uppgifter med hjälp av TMSL-skript. Nya funktioner och funktionaliteter införs snabbt och SSMS uppdateras varje månad.

#### <a name="powershell"></a>PowerShell
Under hanteringsaktiviteter för serverresurser som att skapa servrar, pausa eller återuppta serveråtgärder eller ändra servicenivån används cmdletar för Azure Resource Manager (AzureRM). Under andra aktiviteter för hantering av databaser, till exempel att lägga till eller ta bort rollmedlemmar, behandla eller köra TMSL-skript, används cmdletar i SqlServer-modulen. Både AzureRM- och SQLServer-moduler är tillgängliga i [PowerShell-galleriet](https://www.powershellgallery.com/).


## <a name="your-data-is-secure"></a>Dina data är skyddade
![Datavisualiseringar](./media/analysis-services-overview/aas-overview-secure.png)

#### <a name="authentication"></a>Autentisering
Användarautentisering för Azure Analysis Services hanteras av [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). När användarna försöker logga in på en Azure Analysis Services-databas använder de en organisationskontoidentitet som har åtkomst till databasen. Dessa användaridentiteter måste vara medlemmar i standard-Azure Active Directory för den prenumeration som Azure Analysis Services-servern finns i. Mer information finns i [Autentisering och användarbehörigheter](analysis-services-manage-users.md).

#### <a name="data-security"></a>Datasäkerhet
Azure Analysis Services använder Azure Blob Storage för lagring och metadata för Analysis Services-databaser. Datafiler i Blob krypteras med Azure Blob Server Side Encryption (SSE). När du använder läget Direct Query lagras endast metadata. Faktiska data hämtas från datakällan när frågan körs.

#### <a name="on-premises-data-sources"></a>Lokala datakällor
Du kan skydda åtkomsten till data som förvaras lokalt inom din organisation genom att installera och konfigurera en [Lokal datagateway](analysis-services-gateway.md). Gateways ger åtkomst till data för både Direct Query- och InMemory-läget. När en Azure Analysis Services-modell ansluter till en lokal datakälla skapas en fråga tillsammans med de krypterade autentiseringsuppgifterna för den lokala datakällan. Gateway-molntjänsten analyserar frågan och skickar begäran till en Azure Service Bus. Den lokala gatewayen avsöker Azure Service Bus för väntande begäranden. Gatewayen hämtar sedan frågan, dekrypterar autentiseringsuppgifterna och ansluter till datakällan för körning. Resultatet skickas sedan från datakällan, tillbaka till gatewayen och sedan vidare till Azure Analysis Services-databasen.

Azure Analysis Services lyder under [villkoren för Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) och [sekretesspolicyn för Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Mer information om säkerhet i Azure finns på [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="supports-the-latest-client-tools"></a>Stöder de senaste klientverktygen
![Datavisualiseringar](./media/analysis-services-overview/aas-overview-clients.png)

Moderna datautforsknings- och visualiseringsverktyg som Power BI, Excel och verktyg från tredje part ger användarna interaktiva och högvisuella insikter om dina modelldata.

Klienterna använder [klientbibliotek](analysis-services-data-providers.md) av typen MSOLAP, AMO eller ADOMD för att ansluta till Analysis Services-servrar. Microsoft-klientprogram som Power BI Desktop och Excel installerar samtliga tre klientbibliotek. Men tänk på att, beroende på version eller hur ofta den uppdateras, kanske klientbiblioteken inte har de senaste versioner som krävs av Azure Analysis Services. Detsamma gäller för anpassade program eller andra gränssnitt som AsCmd, TOM, ADOMD.NET. Dessa program kräver vanligtvis att biblioteken installeras manuellt som en del av ett paket.


## <a name="get-help"></a>Få hjälp

#### <a name="documentation"></a>Dokumentation
Det är enkelt att konfigurera och hantera Azure Analysis Services. Här finns all information du behöver för att skapa och hantera servertjänsterna. När du skapar en datamodell som du ska distribuera till en server fungerar det ungefär som när du skapar en datamodell och distribuerar den på en lokal server. Det finns ett omfattande bibliotek med idéskisser, procedurer, självstudiekurser och referensartiklar på [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### <a name="videos"></a>Videoklipp
Kolla in de användbara videoklippen på [Azure Analysis Services på Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### <a name="blogs"></a>Bloggar
Saker och ting ändras snabbt. Du kan alltid hitta den senaste informationen i [Analysis Services-teambloggen](https://blogs.msdn.microsoft.com/analysisservices/) och i [Azure-bloggen](https://azure.microsoft.com/blog/).

#### <a name="community"></a>Community
Analysis Services har ett levande användarforum. Delta i konversationen i [Azure Analysis Services-forumet](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Feedback
Har du förslag eller saknar du någon funktion? Lämna dina kommentarer på [Azure Analysis Services-feedback](https://aka.ms/azureanalysisservicesfeedback).

Har du förslag angående dokumentationen? Du kan lägga till kommentarer med hjälp av Livefyre längst ned i varje artikel.

## <a name="next-steps"></a>Nästa steg
Nu när du vet mer om Azure Analysis Services är det dags att komma igång. Lär dig [att skapa en server](analysis-services-create-server.md) i Azure. När servern är klar går du igenom [Adventure Work-självstudiekursen](tutorials/aas-adventure-works-tutorial.md) som visar hur du skapar en fullt fungerande tabellmodell och distribuerar den till servern.

