---
title: Hantera Azure AI Gallery-data
titleSuffix: ML Studio (classic) - Azure
description: Du kan exportera och ta bort användardata i produkten från Azure AI Gallery med hjälp av gränssnittet eller API:et för AI Gallery Catalog. Den här artikeln visar hur du gör.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 05/25/2018
ms.reviewer: jmartens, mldocs
ms.openlocfilehash: 03341b9e663398f2c42266dead0d2dd01e97c3f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204553"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visa och ta bort användardata i produkten från Azure AI Gallery

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Du kan visa och ta bort användardata i produkten från Azure AI Gallery med hjälp av gränssnittet eller API:et för AI-gallerikatalog. I den här artikeln får du lära dig hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visa dina data i AI Gallery med användargränssnittet

Du kan visa objekt som du har publicerat via azure AI Gallery webbplatsgränssnittet. Användare kan visa både offentliga och onoterade lösningar, projekt, experiment och andra publicerade objekt:

1.    Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klicka på profilbilden i det övre högra hörnet och sedan kontonamnet för att läsa in profilsidan.
3.    På profilsidan visas alla objekt som publicerats i galleriet, inklusive okooterade poster.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Använda API:et för AI-gallerikatalog för att visa dina data

Du kan programmässigt visa data som samlats in via API:et för AI Gallery Catalog, som är tillgängligt på https://catalog.cortanaanalytics.com/entities. Om du vill visa data behöver du ditt författar-ID. Om du vill visa olistade entiteter via katalog-API:et behöver du en åtkomsttoken.

Katalogsvar returneras i JSON-format.

### <a name="get-an-author-id"></a>Skaffa ett redigerings-ID
Författarna-ID:n baseras på den e-postadress som används vid publicering till Azure AI Gallery. Det förändras inte:

1.    Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klicka på profilbilden i det övre högra hörnet och sedan kontonamnet för att läsa in profilsidan.
3.    Url:en i adressfältet visar det alfanumeriska ID:et som följer `authorId=`. Till exempel för webbadressen:`https://gallery.azure.ai/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Författare-ID:`99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Hämta din åtkomsttoken

Du behöver en åtkomsttoken för att visa olistade entiteter via katalog-API:et. Utan en åtkomsttoken kan användare fortfarande visa offentliga entiteter och annan användarinformation.

För att få en åtkomsttoken `DataLabAccessToken` måste du granska huvudet på en HTTP-begäran som webbläsaren gör till katalog-API:et när du är inloggad:

1.    Logga in på [Azure AI Gallery](https://gallery.azure.ai/).
2.    Klicka på profilbilden i det övre högra hörnet och sedan kontonamnet för att läsa in profilsidan.
3.    Öppna fönstret Utvecklarverktyg i webbläsaren genom att trycka på F12, välja fliken Nätverk och uppdatera sidan. 
4. Filtrera begäranden på *strängkatalogen* genom att skriva in i textrutan Filter.
5.    I begäranden `https://catalog.cortanaanalytics.com/entities`till URL:en hittar du en GET-begäran och väljer fliken *Rubriker.* Bläddra ned till avsnittet *Begär sidhuvuden.*
6.    Under rubriken `DataLabAccessToken` finns den alfanumeriska token. Dela inte den här token för att skydda dina data.

### <a name="view-user-information"></a>Visa användarinformation
Visa information i en användares profil med hjälp av det författarnad-ID som du fick i föregående steg: `[AuthorId]`

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Den här URL-begäran:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Returnerar ett svar som:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Visa offentliga enheter

Katalog-API:et lagrar information om publicerade entiteter i Azure AI Gallery som du också kan visa direkt på [AI Gallery-webbplatsen](https://gallery.azure.ai/). 

Om du vill visa publicerade entiteter besöker du följande URL och ersätter `[AuthorId]` med det författar-ID som erhållits i Hämta ett [författar-ID](#get-an-author-id) ovan.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Ett exempel:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Visa onoterade och offentliga enheter

Den här frågan visar endast offentliga entiteter. Om du vill visa alla dina entiteter, inklusive onoterade, anger du åtkomsttoken som hämtats från föregående avsnitt.

1.    Med hjälp av ett verktyg som [Postman](https://www.getpostman.com)skapar du en HTTP GET-begäran till katalog-URL:en enligt beskrivningen i [Hämta åtkomsttoken](#get-your-access-token).
2.    Skapa ett HTTP-begärandehuvud med namnet `DataLabAccessToken`, med värdet inställt på åtkomsttoken.
3.    Skicka HTTP-begäran.

> [!TIP]
> Om olistade entiteter inte visas i svar från katalog-API:et kan användaren ha en ogiltig eller utgången åtkomsttoken. Logga ut från Azure AI Gallery och upprepa sedan stegen i [Hämta åtkomsttoken](#get-your-access-token) för att förnya token. 
