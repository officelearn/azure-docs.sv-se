---
title: Azure Government webb, mobil och API | Microsoft Docs
description: Detta ger en jämförelse av funktioner och vägledning om hur du utvecklar program för Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: gsacavdm
manager: pathuff
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/22/2019
ms.author: gsacavdm
ms.openlocfilehash: 15c6936b7a8c319c4ddef86eddc47546966782b4
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78396352"
---
# <a name="azure-government-web--mobile"></a>Azure Government Webb och mobilt

Den här artikeln beskriver webb-och mobil tjänster för Azure Governments miljön, som beskriver funktions variationer som skiljer sig från den offentliga versionen, samt eventuella miljö specifika överväganden.

## <a name="azure-cognitive-search"></a>Azure Cognitive Search

[Azure kognitiv sökning](https://docs.microsoft.com/azure/search/) är allmänt tillgängligt i Azure Government. För en självriktad utforskning av Sök funktioner med hjälp av offentliga myndighets data, besök webbplatsen för [innehålls sökning och information](https://documentsearch.azurewebsites.net/home/) , Välj data UPPSÄTTNINGEN "amerikansk domstol för överklagande" 1 "och välj sedan ett av demonstrations alternativen.

Sök funktioner som har varit mycket användbara i myndighets Sök program är [kognitiva kunskaper](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro), användbara för att extrahera struktur och information från stora, ej differentierade text dokument.

Grundläggande frågesyntax, som utvärderar frågor för att söka efter stora mängder innehåll, är också relevant för programutvecklare. Azure Kognitiv sökning stöder två syntaxer: [Simple](https://docs.microsoft.com/azure/search/query-simple-syntax) och [full](https://docs.microsoft.com/azure/search/query-lucene-syntax). Du kan granska [exempel på frågeuttryck](https://docs.microsoft.com/azure/search/search-query-simple-examples) för en orientering.

### <a name="variations"></a>Olika
Slut punkten för Sök tjänster som skapats i Azure Government är följande:

| Tjänsttyp | Azure Public | Azure Government |
| ------------ | ------------ | ---------------- |
| Azure Kognitiv sökning-tjänst |\*. search.windows.net |\*. search.windows.us|

Alla allmänt tillgängliga och för hands versions funktioner i det offentliga molnet är också tillgängliga i Azure Government.

### <a name="considerations"></a>Överväganden

Följande information identifierar Azure Government gränserna för Azure Kognitiv sökning:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| ----------------------------------- | --------------------------------------- |
| Alla data som lagras och bearbetas i Azure Kognitiv sökning kan innehålla Azure Government-reglerade data. | Azure Kognitiv sökning metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din tjänst. Ange inte reglerade/kontrollerade data i följande fält: prenumerations namn, resurs grupper, tjänst namn, resurs namn, resurs koder eller objekt som du namnger eller beskriver i Kognitiv sökning (index, indexerare, data källor, synonym kartor och färdighetsuppsättningar). Ta inte med känsliga data i HTTP-huvuden som skickas till REST API PR i Sök-/frågesträngs strängar som skickas som en del av API: et.|

## <a name="app-services"></a>App Services
### <a name="variations"></a>Olika
Azure App-tjänster är allmänt tillgängliga i Azure Government.

Adressen för Azure App Service appar som skapats i Azure Government skiljer sig från de appar som skapats i det offentliga molnet:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
| App Service |\*. azurewebsites.net |\*. azurewebsites.us|
| Tjänstens huvud namns-ID| abfa0a7c-a6b6-4736-8310-5855508787cd | 6a02c803-dafd-4136-b4c3-5a6f318b4714 |

Vissa App Service funktioner som är tillgängliga i det offentliga molnet är ännu inte tillgängliga i Azure Government:

- Diagnostisera och lösa problem
- Distribution
    - Distributions alternativ: endast lokal git-lagringsplats och extern lagrings plats är tillgängliga.
    - Distributions Center
- Inställningar
    - Application Insights
- Utvecklingsverktyg
    - Prestandatest
    - Resurs läsaren
    - PHP-felsökning
- Support & fel sökning
    - App Service Advisor
- App Service Certificate


### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för App Service:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
| Data som anges, lagras och bearbetas i Azure App Service kan innehålla exporterade data. Binärfiler körs inom Azure App Service. Statiska autentiserare, till exempel lösen ord och smartkort för åtkomst till Azures plattforms komponenter. Privata nycklar för certifikat som används för att hantera Azure-plattforms komponenter. SQL-anslutningssträngar. Annan säkerhets information/hemligheter, till exempel certifikat, krypterings nycklar, huvud nycklar och lagrings nycklar som lagras i Azure-tjänster. |Metadata får inte innehålla exporterade data. Dessa metadata innehåller alla konfigurations data som angavs när du skapar och underhåller din Azure App Service. Ange inte reglerade/kontrollerade data i följande fält: resurs grupper, resurs namn, resurs koder|

## <a name="api-management"></a>API Management
Mer information om den här tjänsten och hur du använder den finns i [Azure API Management-dokumentationen](../api-management/index.yml).

### <a name="variations"></a>Olika

Azure API Management-tjänsten är allmänt tillgänglig i Azure Government. Funktioner som för närvarande inte är tillgängliga i API Management tjänst för Azure Government är:

- Azure AD B2C-integrering 
    - Integrering med Azure AD B2C är inte tillgänglig i Azure Government 

URL: erna för åtkomst till Azure-API Management i Azure Government är olika:

| Tjänsttyp | Azure Public | Azure Government |
| --- | --- | --- |
|API Management Gateway| \*. azure-api.net| \*. azure-api.us|
|API Management Portal | \*. portal.azure-api.net |\*. portal.azure-api.us| 
|API Management hantering| \*. management.azure-api.net |\*. management.azure-api.us|

### <a name="considerations"></a>Överväganden
Följande information identifierar Azure Government gränser för Azure API Management-tjänsten:

| Reglerade/kontrollerade data tillåts | Reglerade/kontrollerade data tillåts inte |
| --- | --- |
|Alla data som lagras och bearbetas i Azure API Management-tjänsten kan innehålla Azure Government-reglerade data.|Azure API Management Service-metadata får inte innehålla exporterade data. Ange inte reglerade/kontrollerade data i följande fält: API Management tjänst namn, prenumerations namn, resurs grupper, resurs koder.|

## <a name="next-steps"></a>Nästa steg
För kompletterande information och uppdateringar, prenumerera på [Microsoft Azure Government blogg.](https://blogs.msdn.microsoft.com/azuregov/)

