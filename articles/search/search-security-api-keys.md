---
title: Skapa, hantera och skydda api-nycklar för administratörer och frågor
titleSuffix: Azure Cognitive Search
description: En api-nyckel styr åtkomsten till tjänstens slutpunkt. Administratörsnycklar ger skrivbehörighet. Frågenycklar kan skapas för skrivskyddad åtkomst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 68a17b8b3587077222a9ed2057927c8f16253c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794375"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Skapa och hantera api-nycklar för en Azure Cognitive Search-tjänst

Alla begäranden till en söktjänst behöver en skrivskyddad api-nyckel som har genererats specifikt för din tjänst. Api-nyckeln är den enda mekanismen för att autentisera åtkomst till slutpunkten för söktjänsten och måste inkluderas på varje begäran. I [REST-lösningar](search-get-started-postman.md)anges api-nyckeln vanligtvis i ett begärandehuvud. I [.NET-lösningar](search-howto-dotnet-sdk.md#core-scenarios)anges ofta en nyckel som en konfigurationsinställning och skickas sedan som [autentiseringsuppgifter](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (administratörsnyckel) eller [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (frågenyckel) på [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Nycklar skapas med söktjänsten under tillhandahållandet av tjänster. Du kan visa och hämta nyckelvärden i [Azure-portalen](https://portal.azure.com).

![Portalsida, avsnittet Inställningar, Nycklar](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Vad är en api-nyckel

En api-nyckel är en sträng som består av slumpmässigt genererade siffror och bokstäver. Genom [rollbaserade behörigheter](search-security-rbac.md)kan du ta bort eller läsa nycklarna, men du kan inte ersätta en nyckel med ett användardefinierat lösenord eller använda Active Directory som den primära autentiseringsmetoden för åtkomst till sökåtgärder. 

Två typer av nycklar används för att komma åt söktjänsten: admin (läs-och skrivskyddad) och fråga (skrivskyddad).

|Nyckel|Beskrivning|Begränsningar|  
|---------|-----------------|------------|  
|Admin|Ger fullständiga rättigheter till alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor.<br /><br /> Två administratörsnycklar, så kallade *primära* och *sekundära* nycklar i portalen, genereras när tjänsten skapas och kan återskapas individuellt på begäran. Med två nycklar kan du rulla över en nyckel när du använder den andra nyckeln för fortsatt åtkomst till tjänsten.<br /><br /> Administratörsnycklar anges bara i HTTP-begäranden. Du kan inte placera en api-nyckel för administratörer i en URL.|Högst 2 per tjänst|  
|Söka i data|Ger skrivskyddad åtkomst till index och dokument och distribueras vanligtvis till klientprogram som utfärdar sökbegäranden.<br /><br /> Frågenycklar skapas på begäran. Du kan skapa dem manuellt i portalen eller programmässigt via [REST API:et för hantering](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Frågenycklar kan anges i ett HTTP-begärandehuvud för sökning, förslag eller uppslag. Du kan också skicka en frågenyckel som en parameter på en URL. Beroende på hur klientprogrammet formulerar begäran kan det vara enklare att skicka nyckeln som frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2019-05-06&api-key=[query key]`|50 per tjänst|  

 Visuellt finns det ingen skillnad mellan en administratörsnyckel eller frågenyckel. Båda tangenterna består av 32 slumpmässigt genererade alfanumeriska tecken. Om du tappar bort vilken typ av nyckel som anges i programmet kan du [kontrollera nyckelvärdena i portalen](https://portal.azure.com) eller använda [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) för att returnera värdet och nyckeltypen.  

> [!NOTE]  
>  Det anses vara en dålig säkerhetspraxis att `api-key` skicka känsliga data, till exempel en i URI-begäran. Därför accepterar Azure Cognitive Search bara en `api-key` frågenyckel som en i frågesträngen, och du bör undvika att göra det om inte innehållet i indexet ska vara allmänt tillgängligt. Som en allmän regel rekommenderar `api-key` vi att du skickar din som en begäran header.  

## <a name="find-existing-keys"></a>Hitta befintliga nycklar

Du kan hämta åtkomstnycklar i portalen eller via [REST-API:et för hantering](https://docs.microsoft.com/rest/api/searchmanagement/). Mer information finns i [Hantera api-nycklar för administratörer och frågor](search-security-api-keys.md).

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Lista [söktjänsterna](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
3. Välj tjänsten och klicka på **Inställningar** >**för** att visa administratörs- och frågenycklar på sidan Översikt.

   ![Portalsida, avsnittet Inställningar, Nycklar](media/search-security-overview/settings-keys.png)

## <a name="create-query-keys"></a>Skapa frågenycklar

Frågenycklar används för skrivskyddad åtkomst till dokument inom ett index för åtgärder som är inriktade på en dokumentsamling. Sök-, filter- och förslagsfrågor är alla åtgärder som tar en frågenyckel. Alla skrivskyddade åtgärder som returnerar systemdata eller objektdefinitioner, till exempel en indexdefinition eller indexerarstatus, kräver en administratörsnyckel.

Att begränsa åtkomst och åtgärder i klientappar är viktigt för att skydda sökresurserna på din tjänst. Använd alltid en frågenyckel i stället för en administratörsnyckel för alla frågor som kommer från en klientapp.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Lista [söktjänsterna](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
3. Välj tjänsten och klicka på **Inställningar** >**på**sidan Översikt .
4. Klicka på **Hantera frågenycklar**.
5. Använd frågenyckeln som redan har genererats för tjänsten eller skapa upp till 50 nya frågenycklar. Standardfrågenyckeln namnges inte, men ytterligare frågenycklar kan namnges för hanterbarhet.

   ![Skapa eller använda en frågenyckel](media/search-security-overview/create-query-key.png) 

> [!Note]
> Ett kodexempel som visar användning av frågenyckel finns i [Fråga ett Azure Cognitive Search-index i C#](search-query-dotnet.md).

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Återskapa adminnycklar

Två administratörsnycklar skapas för varje tjänst så att du kan rotera en primärnyckel med hjälp av den sekundära nyckeln för affärskontinuitet.

1. Kopiera den sekundära nyckeln på sidan >**Inställningsnycklar.** **Settings**
2. För alla program uppdaterar du api-tangentinställningarna för att använda den sekundära nyckeln.
3. Återskapa primärnyckeln.
4. Uppdatera alla program för att använda den nya primärnyckeln.

Om du av misstag återskapar båda nycklarna samtidigt misslyckas alla klientbegäranden som använder dessa nycklar med HTTP 403 Forbidden. Innehållet tas dock inte bort och du är inte utelåst permanent. 

Du kan fortfarande komma åt tjänsten via portalen eller hanteringslagret[(REST API](https://docs.microsoft.com/rest/api/searchmanagement/), [PowerShell](https://docs.microsoft.com/azure/search/search-manage-powershell)eller Azure Resource Manager). Hanteringsfunktioner fungerar via ett prenumerations-ID inte en tjänst api-nyckel, och därmed fortfarande tillgänglig även om dina api-nycklar inte är. 

När du har skapat nya nycklar via portal- eller hanteringslager återställs åtkomsten till innehållet (index, indexerare, datakällor, synonymkartor) när du har de nya nycklarna och anger nycklarna på begäranden.

## <a name="secure-api-keys"></a>Säkra api-nycklar
Nyckelsäkerhet säkerställs genom att begränsa åtkomsten via portal- eller Resurshanteraren-gränssnitten (PowerShell eller kommandoradsgränssnitt). Som nämnts kan prenumerationsadministratörer visa och återskapa alla api-nycklar. Som en försiktighetsåtgärd bör du granska rolltilldelningar för att förstå vem som har åtkomst till administratörsnycklarna.

+ Klicka på **Åtkomstkontroll (IAM)** på instrumentpanelen för tjänsten och sedan på fliken **Rolltilldelning för** att visa rolltilldelningar för tjänsten.

Medlemmar i följande roller kan visa och återskapa nycklar: Ägare, Deltagare, [Söktjänstdeltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> För identitetsbaserad åtkomst via sökresultat kan du skapa säkerhetsfilter för att trimma resultat efter identitet och ta bort dokument som beställaren inte ska ha åtkomst till. Mer information finns i [Säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Säker med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Se även

+ [Rollbaserad åtkomstkontroll i Azure Cognitive Search](search-security-rbac.md)
+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda- och optimeringsartikel](search-performance-optimization.md)