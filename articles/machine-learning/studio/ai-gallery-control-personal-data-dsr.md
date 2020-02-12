---
title: Hantera Azure AI Gallery data
titleSuffix: ML Studio (classic) - Azure
description: 'Du kan exportera och ta bort i produkten användardata från Azure AI-galleriet med hjälp av gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 8406da7a429fb5b9429845628a00cdb6cc715540
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77148701"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visa och ta bort i produkten användardata från Azure AI-galleriet

Du kan visa och ta bort i produkten användardata från Azure AI-galleriet med hjälp av gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visa dina data i AI-galleriet med Användargränssnittet

Du kan visa objekt som du har publicerat via webbplatsen Azure AI-galleriet Användargränssnittet. Användare kan visa både offentliga och olistade lösningar, projekt, experiment och andra publicerade objekt:

1.  Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  Profilsidan visas alla objekt som publicerats i galleriet, inklusive olistade poster.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Använd AI-galleriet Catalog-API: et för att visa dina data

Du kan visa data som samlas in via program mässigt i AI-galleriets katalog-API, som är tillgängligt på https://catalog.cortanaanalytics.com/entities. Om du vill visa data som du behöver ditt författare-ID. Om du vill visa olistade myndigheter via Catalog-API: et, behöver du en åtkomsttoken.

Katalogen svar returneras i JSON-format.

### <a name="get-an-author-id"></a>Hämta ett författare-ID
ID: T för författaren baseras på den e-postadress som används vid publicering till Azure AI-galleriet. Den ändrar inte:

1.  Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  URL: en i adress fältet visar det alfanumeriska ID: t efter `authorId=`. Till exempel för URL: en: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Författar-ID: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Få din åtkomsttoken

Du behöver en åtkomsttoken för att visa olistade myndigheter via Catalog-API: et. Utan ett åtkomsttoken, kan användare fortfarande visa offentliga entiteter och andra användarinformation.

För att få en åtkomsttoken måste du kontrol lera `DataLabAccessToken` rubriken för en HTTP-begäran som webbläsaren gör till katalog-API: et när du är inloggad:

1.  Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  Öppna webbläsarens utvecklingsverktyg fönster genom att trycka på F12, Välj fliken nätverk och uppdatera sidan. 
4. Filtrera begär anden i sträng *katalogen* genom att skriva i text rutan filter.
5.  I begär anden till URL: en `https://catalog.cortanaanalytics.com/entities`letar du upp en GET-begäran och väljer fliken *sidhuvud* . Rulla ned *till avsnittet begärandehuvuden* .
6.  Under rubriken `DataLabAccessToken` är den alfanumeriska token. För att skydda dina data, dela inte denna token.

### <a name="view-user-information"></a>Visa information om användare
Använd det författar-ID som du fick i föregående steg, Visa information i en användares profil genom att ersätta `[AuthorId]` i följande URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Exempelvis kan den här URL-begäran:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Returnerar ett svar som:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Visa offentliga entiteter

Katalog-API: t lagrar information om publicerade entiteter till Azure AI Gallery som du också kan visa direkt på [AI-galleriets webbplats](https://gallery.azure.ai/). 

Om du vill visa publicerade entiteter går du till följande URL och ersätter `[AuthorId]` med det författar-ID som hämtades i [Hämta ett författar-ID](#get-an-author-id) ovan.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Några exempel:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Visa inte finns i listan och offentliga entiteter

Den här frågan visar endast de offentliga enheter. Om du vill visa alla enheter, inklusive olistade som ger åtkomst token hämtas från föregående avsnitt.

1.  Använd ett verktyg som [Postman](https://www.getpostman.com)och skapa en HTTP GET-begäran till katalog-URL: en enligt beskrivningen i [Hämta din](#get-your-access-token)åtkomsttoken.
2.  Skapa ett HTTP Request-huvud med namnet `DataLabAccessToken`, med värdet inställt på åtkomsttoken.
3.  Skicka HTTP-begäran.

> [!TIP]
> Om olistade entiteter inte visas i svar från Catalog-API: et, användaren kanske har en ogiltig eller har upphört att gälla åtkomst-token. Logga ut från Azure AI Gallery och upprepa sedan stegen i [Hämta](#get-your-access-token) åtkomsttoken för att förnya token. 
