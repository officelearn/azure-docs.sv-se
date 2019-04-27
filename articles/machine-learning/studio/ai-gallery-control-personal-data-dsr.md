---
title: Hantera data från Azure AI-galleriet
titleSuffix: Azure Machine Learning Studio
description: 'Du kan exportera och ta bort i produkten användardata från Azure AI-galleriet med hjälp av gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 44ff2a5b723c086604acf39e9f975deb53759ae1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60752053"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visa och ta bort i produkten användardata från Azure AI-galleriet

Du kan visa och ta bort i produkten användardata från Azure AI-galleriet med hjälp av gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visa dina data i AI-galleriet med Användargränssnittet

Du kan visa objekt som du har publicerat via webbplatsen Azure AI-galleriet Användargränssnittet. Användare kan visa både offentliga och olistade lösningar, projekt, experiment och andra publicerade objekt:

1.  Logga in på den [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  Profilsidan visas alla objekt som publicerats i galleriet, inklusive olistade poster.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Använd AI-galleriet Catalog-API: et för att visa dina data

Du kan visa data som samlas in via AI-galleriet Catalog API, som är tillgänglig på programmässigt https://catalog.cortanaanalytics.com/entities. Om du vill visa data som du behöver ditt författare-ID. Om du vill visa olistade myndigheter via Catalog-API: et, behöver du en åtkomsttoken.

Katalogen svar returneras i JSON-format.

### <a name="get-an-author-id"></a>Hämta ett författare-ID
ID: T för författaren baseras på den e-postadress som används vid publicering till Azure AI-galleriet. Den ändrar inte:

1.  Logga in på [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  Webbadressen i adressfältet visas alfanumeriska ID följande `authorId=`. Till exempel för URL: en: `https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Författare-ID: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Få din åtkomsttoken

Du behöver en åtkomsttoken för att visa olistade myndigheter via Catalog-API: et. Utan ett åtkomsttoken, kan användare fortfarande visa offentliga entiteter och andra användarinformation.

För att få en åtkomsttoken kan du behöva granska den `DataLabAccessToken` en HTTP-begäran som webbläsaren skickar till katalogen API: et när du är inloggad-huvud:

1.  Logga in på den [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och sedan namnet på kontot för att läsa in din profilsida.
3.  Öppna webbläsarens utvecklingsverktyg fönster genom att trycka på F12, Välj fliken nätverk och uppdatera sidan. 
4. Filtrera begäranden på strängen *catalog* genom att skriva i rutan för textfiltrering.
5.  I begäranden till URL: en `https://catalog.cortanaanalytics.com/entities`, hitta en GET-begäran och välj den *rubriker* fliken. Rulla ned till den *begärandehuvuden* avsnittet.
6.  Under rubriken `DataLabAccessToken` är alfanumeriska token. För att skydda dina data, dela inte denna token.

### <a name="view-user-information"></a>Visa information om användare
Med hjälp av författaren-ID som du fick i föregående steg, visa information i en användares profil genom att ersätta `[AuthorId]` i följande URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Exempelvis kan den här URL-begäran:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Returnerar ett svar som:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Visa offentliga entiteter

Catalog-API: et lagrar information om publicerade entiteter i Azure AI-galleriet som du kan också visa direkt på den [AI-galleriet webbplats](https://gallery.azure.ai/). 

Om du vill visa publicerade entiteter finns följande URL ersätter `[AuthorId]` med ID: T för författaren som hämtades i [hämta ett författare ID](#get-an-author-id) ovan.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Exempel:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Visa inte finns i listan och offentliga entiteter

Den här frågan visar endast de offentliga enheter. Om du vill visa alla enheter, inklusive olistade som ger åtkomst token hämtas från föregående avsnitt.

1.  Med ett verktyg som [Postman](https://www.getpostman.com), skapa en HTTP GET-begäran till URL: en för katalogen enligt beskrivningen i [få din åtkomsttoken](#get-your-access-token).
2.  Skapa en HTTP-frågehuvudet kallas `DataLabAccessToken`, med värdet satt till åtkomsttoken.
3.  Skicka HTTP-begäran.

> [!TIP]
> Om olistade entiteter inte visas i svar från Catalog-API: et, användaren kanske har en ogiltig eller har upphört att gälla åtkomst-token. Logga ut från Azure AI-galleriet och sedan upprepa stegen i [få din åtkomsttoken](#get-your-access-token) att förnya token. 
