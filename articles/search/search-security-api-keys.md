---
title: Skapa, hantera och skydda administratör och fråga api-nycklar för Azure Search | Microsoft Docs
description: API-nycklar styra åtkomsten till tjänstslutpunkten. Admin nycklar bevilja skrivbehörighet. Frågan nycklar kan skapas för skrivskyddad åtkomst.
author: HeidiSteen
manager: cgronlun
tags: azure-portal
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/20/2018
ms.author: heidist
ms.openlocfilehash: 4215795b7cd2a25427a3ce9b3cde16bfc69cb009
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-api-keys-for-an-azure-search-service"></a>Skapa och hantera api-nycklar för en Azure Search-tjänst

Alla förfrågningar till en söktjänst måste api-nyckel som har skapats specifikt för din tjänst. Den här api-nyckel är den enda mekanismen för att autentisera åtkomst till Sök-tjänsteslutpunkt. 

En api-nyckel är en sträng som består av slumpmässigt genererat siffror och bokstäver. Via [rollbaserade behörigheter](search-security-rbac.md), kan du ta bort eller läsa nycklarna, men du kan ersätta en nyckel med ett användardefinierat lösenord eller använda Active Directory som primär autentisering-metoder för att komma åt sökningar. 

Två typer av nycklar används för åtkomst till din söktjänst: admin (skrivskyddad) och fråga (skrivskyddad).

|Nyckel|Beskrivning|Begränsningar|  
|---------|-----------------|------------|  
|Administratör|Ger fullständig behörighet till alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor.<br /><br /> Två admin nycklar kallas *primära* och *sekundära* i portalen genereras när tjänsten har skapats och individuellt genereras på begäran. Har två nycklar kan du återställa över en nyckel när du använder den andra nyckeln för fortsatt tillgång till tjänsten.<br /><br /> Admin nycklar endast anges i HTTP-huvuden för begäran. Du kan placera en admin api-nyckel i en URL.|Maximalt 2 per tjänst|  
|Fråga|Ger läsbehörighet till index och dokument och distribueras vanligen till klientprogram som kan utfärda search-begäranden.<br /><br /> Frågan nycklar skapas på begäran. Du kan skapa dem manuellt på portalen eller programmässigt via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/).<br /><br /> Frågan nycklar kan anges i ett HTTP-huvud för begäran för sökning, förslag eller sökning. Du kan också ange en fråga nyckel som en parameter på en URL. Beroende på hur ditt klientprogram formulates begäran, kan det vara enklast att överföra nyckeln som en frågeparameter:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2017-11-11&api-key=[query key]`|50 per tjänst|  

 Visuellt, görs ingen åtskillnad mellan en administrationsnyckeln eller Frågenyckeln. Båda nycklarna genereras strängar som består av 32 slumpmässigt alfanumeriska tecken. Om du tappar bort reda på vilken typ av nyckel har angetts i ditt program kan du [Kontrollera nyckelvärdena i portalen](https://portal.azure.com) eller använda den [REST API](https://docs.microsoft.com/rest/api/searchmanagement/) att returnera värdet och nyckeltyp.  

> [!NOTE]  
>  En dålig säkerhetsrutin att skicka känslig information som anses en `api-key` i URI-begäran. Därför accepterar Azure Search bara en fråga nyckel som en `api-key` i frågan sträng, och du bör undvika att du gör det om innehållet i ditt index måste vara offentligt tillgängliga. Som en allmän regel rekommenderar vi skicka din `api-key` som ett huvud.  

## <a name="find-api-keys-for-your-service"></a>Hitta api-nycklar för tjänsten

Du kan hämta snabbtangenter i portalen eller via den [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/). Mer information finns i [hantera administratör och fråga api-nycklar](search-security-api-keys.md).

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Lista de [söktjänster](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) för din prenumeration.
3. Välj tjänsten och hitta på sidan service **inställningar** >**nycklar** att visa administratör och fråga nycklar.

![Portalsida, inställningar, nycklar avsnitt](media/search-security-overview/settings-keys.png)

## <a name="regenerate-admin-keys"></a>Återskapa admin-nycklar

Två admin nycklar skapas för varje tjänst så att du kan förnya en primär nyckel med hjälp av den sekundära nyckeln för fortsatt tillgång.

Om du återskapar både primära och sekundära nycklarna samtidigt, har de program som använder antingen nyckel för att komma åt tjänsteåtgärder inte längre åtkomst till tjänsten.

1. I den **inställningar** >**nycklar** sidan, kopiera den sekundära nyckeln.
2. Uppdatera inställningarna för api-nyckel om du vill använda den sekundära nyckeln för alla program.
3. Återskapa den primära nyckeln.
4. Uppdatera alla program att använda den nya primärnyckeln.

## <a name="secure-api-keys"></a>Säkra api-nycklar
Nyckelsäkerhet säkerställs genom att begränsa åtkomst via portalen eller Resource Manager-gränssnitt (kommandoradsgränssnittet eller PowerShell). Som nämnts, kan prenumerationsadministratörer visa och återskapa alla api-nycklar. Granska rolltilldelningar för att förstå vem som har åtkomst till admin-nycklar som en säkerhetsåtgärd.

+ I instrumentpanelen för tjänsten klickar du på **åtkomstkontroll (IAM)** att visa rolltilldelningar för din tjänst.

Medlemmar av följande roller kan visa och återskapa nycklar: ägare, deltagare, [Search-tjänsten deltagare](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#search-service-contributor)

> [!Note]
> För identitetsbaserad åtkomst via sökresultat, kan du skapa säkerhetsfilter Beskär resultat av identitet, tar bort dokument som begäranden inte ska ha åtkomst. Mer information finns i [säkerhetsfilter](search-security-trimming-for-azure-search.md) och [Secure med Active Directory](search-security-trimming-for-azure-search-with-aad.md).

## <a name="see-also"></a>Se också

+ [Rollbaserad åtkomstkontroll i Azure Search](search-security-rbac.md)
+ [Hantera med PowerShell](search-manage-powershell.md) 
+ [Prestanda och optimering artikel](search-performance-optimization.md)