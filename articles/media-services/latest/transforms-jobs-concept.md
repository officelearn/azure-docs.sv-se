---
title: Transformeringar och jobb i Azure Media Services | Microsoft Docs
description: När du använder Media Services kan behöva du skapa en transformering för att beskriva regler och specifikationer för bearbetning av dina videor. Den här artikeln ger en översikt över vad transformeringen är och hur du använder den.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: e84f74fe4678a65a33c9cc728f290e7c905b2261
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55743743"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb
 
Använd [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) att konfigurera vanliga uppgifter för kodning eller analysera videor. Varje **transformera** beskriver ett recept eller ett arbetsflöde med uppgifter för att bearbeta video- eller ljudinnehåll filer. En enda transformering kan använda mer än en regel. En transformering kan till exempel ange att varje videon kodas till en MP4-fil på en viss bithastighet och att en miniatyrbild genereras från den första bildrutan i videon. Du skulle lägga till en TransformOutput post för varje regel som du vill ska ingå i din transformeringen. Du kan skapa transformeringar i ditt Media Services-konto med hjälp av Media Services v3-API eller använda någon av de publicerade SDK: er. Media Services-v3 API drivs av Azure Resource Manager, så du kan också använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan användas för att låsa åtkomsten till transformeringar.

Uppdateringsåtgärden på den [transformera](https://docs.microsoft.com/rest/api/media/transforms) entiteten är avsedd för att göra ändringar beskrivningen eller prioriteringarna för underliggande TransformOutputs. Vi rekommenderar att dessa uppdateringar ska utföras när alla pågående jobb har slutförts. Om du vill skriva om receptet, måste du skapa en ny omvandling.

En [jobbet](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Azure Media Services att tillämpa den **omvandla** till en given video- eller ljudinnehåll datainnehållet. När vi har skapats, kan du skicka jobb med hjälp av API: er för Media Services eller någon av de publicerade SDK: er. Den **jobbet** anger information som platsen för indatavideo och platsen för utdata. Du kan ange platsen för dina indata video med: HTTPS-adresser, SAS URL: er eller [tillgångar](https://docs.microsoft.com/rest/api/media/assets). Förlopp och status för jobben kan hämtas genom att övervaka händelser med Event Grid. Mer information finns i [övervaka händelser med hjälp av EventGrid](job-state-events-cli-how-to.md).

Uppdateringsåtgärden på den [jobbet](https://docs.microsoft.com/rest/api/media/jobs) entiteten kan användas för att ändra den *beskrivning*, och *prioritet* egenskaper när jobbet har skickats. En ändring av den *prioritet* egenskap gäller endast om jobbet är fortfarande i en kö. Om jobbet har startat bearbetning eller har slutförts, har ändra prioritet ingen effekt.

> [!NOTE]
> Egenskaper för **transformera** och **jobbet** som är av typen är alltid i UTC-format för datum/tid.

## <a name="typical-workflow"></a>Typiskt arbetsflöde

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering, om du inte planerar att använda den i framtiden. 

### <a name="example"></a>Exempel

Anta att du vill extrahera första bildruta från dina videor som en miniatyrbild – är vad du måste göra: 

1. Definiera receptet eller regeln för att bearbeta dina videor – ”Använd första ramen videoklippets som miniatyren”. 
2. För varje video skulle du instruera tjänsten: 
    1. Var du hittar den här videon  
    2. Var ska skriva på miniatyrbilden för utdata. 

En **transformera** hjälper dig att skapa receptet en gång (steg 1) och skicka jobb med hjälp av det receptet (steg 2).

## <a name="paging"></a>Sidindelning

Se [filtrering, sortering, växling av Media Services entiteter](entities-overview.md).

## <a name="next-steps"></a>Nästa steg

[Ladda upp, koda och strömma videofiler](stream-files-tutorial-with-api.md)
