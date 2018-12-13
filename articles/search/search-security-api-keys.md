---
title: Skapa, hantera och skydda admin och fråga api-nycklar – Azure Search
description: API-nycklarna styr åtkomsten till tjänsteslutpunkt. Administratörsnycklar beviljar skrivbehörighet. Frågenycklar kan skapas för skrivskyddad åtkomst.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 6ba63fa776e92dd2f8035cfbbdb8cea2860d106f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316941"
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Skapa och hantera api-nycklar för en Azure Search-tjänst

Alla förfrågningar till en söktjänst måste en skrivskyddad api-nyckel som har skapats specifikt för din tjänst. Api-nyckeln är den enda mekanismen för att autentisera åtkomst till search tjänstens slutpunkt och måste tas för varje begäran. I [REST lösningar](search-get-started-nodejs.md#update-the-configjs-with-your-search-service-url-and-api-key), api-nyckeln anges vanligtvis i en rubrik för begäran. I [.NET-lösningar](search-howto-dotnet-sdk.md#core-scenarios), en nyckel anges ofta som en konfigurationsinställning och sedan skickas som [autentiseringsuppgifter](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.credentials) (admin-nyckel) eller [SearchCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.searchcredentials) (frågenyckel) på [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient).

Nycklarna skapas med din söktjänst vid etableringen av tjänsten. Du kan visa och hämta nyckelvärden i den [Azure-portalen](https://portal.azure.com).

![Portalsidan inställningar, nycklar-avsnittet](media/search-manage/azure-search-view-keys.png)

## <a name="what-is-an-api-key"></a>Vad är en api-nyckel

En api-nyckel är en sträng som består av slumpmässigt genererat siffror och bokstäver. Via [rollbaserade behörigheter](search-security-rbac.md), kan du ta bort eller läsa nycklarna, men du kan inte ersätta en nyckel med en användardefinierad lösenord eller använda Active Directory som primär autentisering-metod för att komma åt sökningar. 

Två typer av nycklar som används för att få åtkomst till din söktjänst: admin (skrivskyddad) och fråga (skrivskyddad).

|Nyckel|Beskrivning|Begränsningar|  
|---------|-----------------|------------|  
|Administratör|Bevilja fullständiga rättigheter för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor.<br /><br /> Två administratörsnycklar kallas *primära* och *sekundära* nycklar i portalen genereras när tjänsten har skapats och individuellt kan återskapas på begäran. Har två nycklar kan du förnyar en nyckel medan den andra nyckeln för fortsatt åtkomst till tjänsten.<br /><br /> Administratörsnycklar endast anges i HTTP-begärans sidhuvud. Du kan inte placera en admin api-nyckel i en URL.|Högst 2 per tjänst|  
|Söka i data|Ger läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.<br /><br /> Frågenycklar skapas på begäran. Du kan skapa dem manuellt i portalen eller via programmering via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Frågenycklar kan anges i en HTTP-frågehuvudet vid sökning, förslag eller lookup-åtgärden. Du kan också skicka en frågenyckel som en parameter på en URL. Beroende på hur ditt klientprogram formulates begäran, kan det vara enklare att överföra nyckeln som frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 per tjänst|  

 Visuellt, görs ingen åtskillnad mellan en admin-nyckel eller en frågenyckel. Båda nycklarna är strängar som består av 32 slumpmässigt genererade alfanumeriska tecken. Om du förlorar reda på vilken typ av nyckel har angetts i ditt program kan du [Kontrollera nyckelvärdena i portalen](https://portal.azure.com) eller Använd den [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) att returnera värdet och nyckeltyp.  

> [!NOTE]  
>  Det anses vara en dålig säkerhetsrutin att skicka känsliga data som en `api-key` i begärande-URI. Därför accepterar Azure Search endast en frågenyckel som en `api-key` i frågan sträng, och du bör undvika att göra det. Om inte innehållet i ditt index bör vara offentligt tillgängliga. Som en allmän regel rekommenderar vi att dina `api-key` som en rubrik för begäran.  

## <a name="find-api-keys-for-your-service"></a>Hitta api-nycklar för din tjänst

Du kan hämta åtkomstnycklar i portalen eller via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/). Mer information finns i [hantera api-nycklar som administratör och fråga](search-security-api-keys.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Lista de [söktjänster](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
3. Välj tjänsten och på sidan, hitta **inställningar** >**nycklar** att visa admin och fråga nycklar.

![Portalsidan inställningar, nycklar-avsnittet](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Återskapa administratörsnycklar

Två administratörsnycklar skapas för varje tjänst så att du kan förnya en primär nyckel med hjälp av den sekundära nyckeln för fortsatt åtkomst.

Om du återskapar både primära och sekundära nycklarna samtidigt kan kommer de program som använder någon av nycklarna för åtkomst till tjänståtgärder inte längre åtkomst till tjänsten.

1. I den **inställningar** >**nycklar** sidan, kopiera den sekundära nyckeln.
2. Uppdatera inställningarna för api-nyckel om du vill använda den sekundära nyckeln för alla program.
3. Återskapa den primära nyckeln.
4. Uppdatera alla program du använder den nya primära nyckeln.

## <a name="secure-api-keys"></a>Säkra api-nycklar
Nyckelsäkerhet säkerställs genom att begränsa åtkomst via portalen eller Resource Manager-gränssnitt (PowerShell eller kommandoradsgränssnittet). Enligt vad som anges, kan prenumerationens administratörer visa och återskapa alla api-nycklar. Granska rolltilldelningar för att förstå vem som har åtkomst till admin-nycklarna som en försiktighetsåtgärd.

+ I instrumentpanelen för tjänsten klickar du på **åtkomstkontroll (IAM)** och sedan den **rolltilldelningar** fliken för att visa rolltilldelningar för din tjänst.

Medlemmar i följande roller kan visa och återskapa nycklar: Ägare, deltagare, [Sök Service deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> För identitetsbaserad åtkomst via sökresultat, kan du skapa säkerhetsfilter för att trimma resultat av identitet, ta bort dokument som begäranden inte ska ha åtkomst. Mer information finns i [säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Secure med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Se också

+ [Rollbaserad åtkomstkontroll i Azure Search](search-security-rbac.md)
+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering artikeln](search-performance-optimization.md)