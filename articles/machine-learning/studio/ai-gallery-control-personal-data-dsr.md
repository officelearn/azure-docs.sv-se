---
title: Visa och ta bort dina data från Azure AI-galleriet | Microsoft Docs
description: 'Du kan exportera och ta bort i produkten användardata från Azure AI-galleriet-gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.'
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
manager: cgronlun
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: studio
ms.topic: conceptual
ms.date: 05/25/2018
ms.openlocfilehash: bc6ffa912d7914c8662dbde623e04947540ac149
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655613"
---
# <a name="view-and-delete-in-product-user-data-from-azure-ai-gallery"></a>Visa och ta bort i produkten användardata från Azure AI-galleriet

Du kan visa och ta bort i produkten användardata från Azure AI-galleriet-gränssnittet eller AI-galleriet Catalog API: et. Den här artikeln visar hur du gör.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="view-your-data-in-ai-gallery-with-the-ui"></a>Visa data i AI-galleriet med Användargränssnittet

Du kan visa objekt som du har publicerat via webbplatsen Azure AI-galleriet Användargränssnittet. Användare kan visa både offentliga och olistade lösningar, projekt, experiment och andra publicerade objekt:

1.  Logga in på den [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och namnet på kontot för att läsa in profilsidan.
3.  Profilsidan visas alla objekt som har publicerats i galleriet, inklusive olistade poster.

## <a name="use-the-ai-gallery-catalog-api-to-view-your-data"></a>Använda AI-galleriet Catalog-API: et för att visa dina data

Du kan visa data som samlas in via AI-galleriet katalog API, som kan nås på programmässigt https://catalog.cortanaanalytics.com/entities. Om du vill visa data som du behöver författare-ID. Om du vill visa olistade enheter via Catalog-API: et, behöver du en åtkomst-token.

Katalogen svar returneras i JSON-format.

### <a name="get-an-author-id"></a>Hämta en författare-ID
ID som författare baseras på e-postadressen används vid publicering till Azure AI-galleriet. Ändringen inte:

1.  Logga in på [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och namnet på kontot för att läsa in profilsidan.
3.  URL-Adressen i adressfältet visas alfanumeriska ID följande `authorId=`. Till exempel för URL: `https://gallery.cortanaintelligence.com/Home/Author?authorId=99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`
        
    Författare-ID: `99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA`

### <a name="get-your-access-token"></a>Få åtkomst-token

Du behöver en åtkomsttoken att visa olistade enheter via Catalog-API: et. Utan en åtkomst-Token, kan användarna fortfarande visa offentliga entiteter och andra användarinformation.

Om du vill få en åtkomsttoken, måste du kontrollera den `DataLabAccessToken` rubriken för en HTTP-begäran som webbläsaren gör Catalog-API: et när du är inloggad:

1.  Logga in på den [Azure AI-galleriet](https://gallery.azure.ai/).
2.  Klicka på profilbild i det övre högra hörnet och namnet på kontot för att läsa in profilsidan.
3.  Öppna fönstret webbläsarens utvecklingsverktyg genom att trycka på F12, Välj fliken nätverk och uppdatera sidan. 
4. Filtrering av begäranden på strängen *katalog* genom att skriva i Filter-textrutan.
5.  I begäranden till URL: en `https://catalog.cortanaanalytics.com/entities`, hitta en GET-begäran och väljer den *huvuden* fliken. Rulla ned till den *begärandehuvuden* avsnitt.
6.  Under rubriken `DataLabAccessToken` är alfanumeriska token. För att skydda dina data, dela inte denna token.

### <a name="view-user-information"></a>Visa information om användare
Använder författare-ID som du fick i föregående steg, visa information i en användares profil genom att ersätta `[AuthorId]` i följande URL:

    https://catalog.cortanaanalytics.com/users/[AuthorID]

Till exempel den här URL-begäran:
    
    https://catalog.cortanaanalytics.com/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA

Returnerar ett svar som:

    {"entities_count":9,"contribution_score":86.351575190956922,"scored_at":"2018-05-07T14:30:25.9305671+00:00","contributed_at":"2018-05-07T14:26:55.0381756+00:00","created_at":"2017-12-15T00:49:15.6733094+00:00","updated_at":"2017-12-15T00:49:15.6733094+00:00","name":"First Last","slugs":["First-Last"],"tenant_id":"14b2744cf8d6418c87ffddc3f3127242","community_id":"9502630827244d60a1214f250e3bbca7","id":"99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA","_links":{"self":"https://catalog.azureml.net/tenants/14b2744cf8d6418c87ffddc3f3127242/communities/9502630827244d60a1214f250e3bbca7/users/99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA"},"etag":"\"2100d185-0000-0000-0000-5af063010000\""}


### <a name="view-public-entities"></a>Visa offentliga enheter

Catalog-API: et lagrar information om publicerade enheter till Azure AI-galleriet som du kan också visa direkt på den [AI-galleriet webbplats](https://gallery.azure.ai/). 

Besök följande URL, om du vill visa publicerade entiteter ersätter `[AuthorId]` med ID: T för författare som erhölls i [hämta författare ID](#get-an-author-ID) ovan.

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '[AuthorId]'

Exempel:

    https://catalog.cortanaanalytics.com/entities?$filter=author/id eq '99F1F5C6260295F1078187FA179FBE08B618CB62129976F09C6AF0923B02A5BA'

### <a name="view-unlisted-and-public-entities"></a>Visa olistade och offentliga enheter

Den här frågan visar endast de offentliga enheter. Ge åtkomst om du vill visa alla enheter, inklusive olistade de token som hämtades i föregående avsnitt.

1.  Med ett verktyg som [Postman](https://www.getpostman.com), skapa en HTTP GET-begäran till URL: en katalog som beskrivs i [få åtkomst-token](#get-your-access-token).
2.  Skapa en HTTP-frågehuvudet kallas `DataLabAccessToken`, med värdet till åtkomsttoken.
3.  Skicka HTTP-begäran.

> [!TIP]
> Om olistade entiteter inte visas i svar från Catalog-API: et, användaren kanske har ett ogiltigt eller har upphört att gälla åtkomst-token. Logga ut från Azure AI-galleriet och sedan upprepa stegen i [få åtkomst-token](#get-your-access-token) förnya token. 
