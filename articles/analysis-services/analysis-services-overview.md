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
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: sv-se
ms.lasthandoff: 06/17/2017


---
# Vad är Azure Analysis Services?
<a id="what-is-azure-analysis-services" class="xliff"></a>
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services, som skapats på den bevisade analytiska motorn i Microsoft SQL Server Analysis Services, ger datamodellering i företagsklass i molnet. 

Kolla in den här videon som visar hur Azure Analysis Services passar in bland Microsofts BI-funktioner och fördelarna med att ha dina datamodeller i molnet.


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## Bygger på SQL Server Analysis Services
<a id="built-on-sql-server-analysis-services" class="xliff"></a>
Azure Analysis Services är kompatibelt med SQL Server Analysis Services Enterprise Edition som du redan känner till. Azure Analysis Services stöder tabellmodeller på kompatibilitetsnivå 1200 eller senare. DirectQuery, partitioner, säkerhet på radnivå, dubbelriktade relationer och översättningar stöds.

## Använd de verktyg som du är van vid
<a id="use-the-tools-you-already-know" class="xliff"></a>
![BI-utvecklarverktyg](./media/analysis-services-overview/aas-overview-dev-tools.png)

När du skapar datamodeller för Azure Analysis Services kan använda du samma verktyg som för SQL Server Analysis Services. Skapa och distribuera modeller med hjälp av [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx). Hantera dina servrar och modelldatabaser med hjälp av [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Och automatisera uppgifter med [Powershell](analysis-services-powershell.md)- och [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)-mallar 

## Stöder de senaste funktionerna
<a id="supports-the-latest-features" class="xliff"></a>
Azure Analysis Services stöder tabellmodeller på kompatibilitetsnivå 1200 och 1400 Preview.

**Tabell 1200** – Inkluderades först i SQL Server 2016 Analysis Services. I 1200 introducerades tabellobjektsmodellen (TOM) för att beskriva modellobjekt som tabeller, kolumner och relationer. Tabellobjektsmodellen exponeras i JSON via [tabellmodellskriptspråket (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) och AMO-datadefinitionsspråket via namnområdet [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx).

**Tabell 1400 Preview** – Introducerar stöd för detaljrader, säkerhet på objektnivå, stöd för ojämna hierarkier, en modern funktion för dataanslutning för att hämta data och många andra förbättringar. För att kunna utnyttja alla de senaste funktionerna måste du använda den senaste versionen av [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx). Eftersom tabell 1400 fortfarande är en förhandsversion kan saker ändras snabbt. Senaste nytt finns i vårt [blogginlägg](https://azure.microsoft.com/blog/1400-models-in-azure-as/).

## Datakällor
<a id="data-sources" class="xliff"></a>
Datamodellerna som distribueras till servrar i Azure stöder anslutning till lokala datakällor i din organisation eller i molnet. Du kan kombinera data från datakällor som finns både lokalt och i molnet. På så sätt får du en BI-hybridlösning.

Eftersom din server finns i molnet går det smidigt att ansluta till datakällor i molnet. När du ansluter till lokala datakällor ser [den lokala datagatewayen](analysis-services-gateway.md) till att du får snabba och säkra anslutningar till servern i molnet. Mer information om vilka lokala datakällor som stöds finns i [Data sources supported in Azure Analysis Services](analysis-services-datasource.md) (Datakällor som stöds i Azure Analysis Services).


## Utforska dina data överallt
<a id="explore-your-data-from-anywhere" class="xliff"></a>
Anslut och hämta data från dina servrar överallt. Azure Analysis Services stöder anslutning från Power BI Desktop, Excel, anpassade appar och webbläsarbaserade verktyg.

![Datavisualiseringar](./media/analysis-services-overview/aas-overview-visualization.png)


## Skydda
<a id="secure" class="xliff"></a>
#### Användarautentisering
<a id="user-authentication" class="xliff"></a>
Användarautentisering för Azure Analysis Services hanteras av [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). När användarna försöker logga in på en Azure Analysis Services-databas använder de en organisationskontoidentitet som har åtkomst till databasen. Dessa användaridentiteter måste vara medlemmar i standard-Azure Active Directory för den prenumeration som Azure Analysis Services-servern finns i. [Katalogintegrering](https://technet.microsoft.com/library/jj573653.aspx) mellan AAD och lokala Active Directory är ett bra sätt att ge användarna åtkomst till en Azure Analysis Services-databas, men det behövs inte för alla scenarier.

Användare loggar in med användarens huvudnamn (UPN) för sina konton och lösenord. Vid synkronisering med en lokal Active Directory är UPN ofta den e-postadress användaren använder i organisationen.

Behörigheter för att hantera Azure Analysis Services-serverresursen hanteras genom att tilldela användare till roller i Azure-prenumerationen. Som standard har prenumerationsadministratörer ägarbehörighet till serverresursen i Azure. Ytterligare användare kan läggas till med hjälp av Azure Resource Manager.

#### Datasäkerhet
<a id="data-security" class="xliff"></a>
Azure Analysis Services använder Azure Blob Storage för lagring och metadata för Analysis Services-databaser. Datafiler i Blob krypteras med Azure Blob Server Side Encryption (SSE). När du använder läget Direct Query lagras endast metadata. Faktiska data hämtas från datakällan när frågan körs.

#### Lokala datakällor
<a id="on-premises-data-sources" class="xliff"></a>
Du kan skydda åtkomsten till data som förvaras lokalt i din organisation genom att installera och konfigurera en [lokal datagateway](analysis-services-gateway.md). Gateways ger åtkomst till data för både Direct Query- och InMemory-läget. När en Azure Analysis Services-modell ansluter till en lokal datakälla skapas en fråga tillsammans med de krypterade autentiseringsuppgifterna för den lokala datakällan. Gateway-molntjänsten analyserar frågan och skickar begäran till en Azure Service Bus. Den lokala gatewayen avsöker Azure Service Bus för väntande begäranden. Gatewayen hämtar sedan frågan, dekrypterar autentiseringsuppgifterna och ansluter till datakällan för körning. Resultatet skickas sedan från datakällan, tillbaka till gatewayen och sedan vidare till Azure Analysis Services-databasen.

Azure Analysis Services lyder under [villkoren för Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) och [sekretesspolicyn för Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Mer information om säkerhet i Azure finns på [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## Få hjälp
<a id="get-help" class="xliff"></a>

### Dokumentation
<a id="documentation" class="xliff"></a>
Det är enkelt att konfigurera och hantera Azure Analysis Services. Här finns all information du behöver för att skapa och hantera en server. När du skapar en datamodell som du ska distribuera till en server fungerar det ungefär som när du skapar en datamodell och distribuerar den på en lokal server. Det finns ett omfattande bibliotek med idéskisser, procedurer, självstudiekurser och referensartiklar på [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services).

### Videoklipp
<a id="videos" class="xliff"></a>
Kolla in de användbara videoklippen på [Azure Analysis Services på Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

### Bloggar
<a id="blogs" class="xliff"></a>
Saker och ting ändras snabbt. Du kan alltid hitta den senaste informationen i [Analysis Services-teambloggen](https://blogs.msdn.microsoft.com/analysisservices/) och i [Azure-bloggen](https://azure.microsoft.com/blog/).

### Community
<a id="community" class="xliff"></a>
Analysis Services har ett levande användarforum. Delta i konversationen i [Azure Analysis Services-forumet](https://aka.ms/azureanalysisservicesforum).

## Feedback
<a id="feedback" class="xliff"></a>
Har du förslag eller saknar du någon funktion? Lämna dina kommentarer på [Azure Analysis Services-feedback](https://aka.ms/azureanalysisservicesfeedback).

Har du förslag angående dokumentationen? Du kan lägga till kommentarer med hjälp av Livefyre längst ned i varje artikel.

## Nästa steg
<a id="next-steps" class="xliff"></a>
Nu när du vet mer om Azure Analysis Services är det dags att komma igång. Lär dig hur du [skapar du en server](analysis-services-create-server.md) i Azure och [distribuerar en tabellmodell](analysis-services-deploy.md) till den.

