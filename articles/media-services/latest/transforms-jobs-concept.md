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
ms.date: 05/08/2019
ms.author: juliako
ms.openlocfilehash: e64e980d42086603c9eb8ce39a96a9766a78afcb
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472466"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb

Det här avsnittet innehåller information om [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) och [jobb](https://docs.microsoft.com/rest/api/media/jobs) och beskriver relationen mellan dessa entiteter. 

## <a name="overview"></a>Översikt 

### <a name="transformsjobs-workflow"></a>Arbetsflöde för transformeringar/jobb

Följande diagram visar transformeringar/jobb arbetsflöde.

![Transformering](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typiskt arbetsflöde

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering, om du inte planerar att använda den i framtiden. 

#### <a name="example"></a>Exempel

Anta att du vill extrahera första bildruta från dina videor som en miniatyrbild – är vad du måste göra: 

1. Definiera receptet eller regeln för att bearbeta dina videor – ”Använd första ramen videoklippets som miniatyren”. 
2. För varje video skulle du instruera tjänsten: 
    1. Var du hittar den här videon  
    2. Var ska skriva på miniatyrbilden för utdata. 

En **transformera** hjälper dig att skapa receptet en gång (steg 1) och skicka jobb med hjälp av det receptet (steg 2).

> [!NOTE]
> Egenskaper för **transformera** och **jobbet** som är av typen är alltid i UTC-format för datum/tid.

## <a name="transforms"></a>Transformering

Använd **omvandlar** att konfigurera vanliga uppgifter för kodning eller analysera videor. Varje **transformera** beskriver ett recept eller ett arbetsflöde med uppgifter för att bearbeta video- eller ljudinnehåll filer. En enda transformering kan använda mer än en regel. En transformering kan till exempel ange att varje videon kodas till en MP4-fil på en viss bithastighet och att en miniatyrbild genereras från den första bildrutan i videon. Du skulle lägga till en TransformOutput post för varje regel som du vill ska ingå i din transformeringen. Du kan använda förinställningar för att berätta för transformering hur inkommande mediefiler ska bearbetas.

Förinställningar finns i Media Services v3, starkt typifierad entiteter i själva API: T. Du kan hitta ”schema” definitionen för dessa objekt i [Open API Specification (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också visa de förinställda definitionerna (t.ex. **StandardEncoderPreset**) i den [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (eller andra Media Services v3 SDK referensdokumentation).

Du kan skapa transformeringar med hjälp av REST, CLI, eller använda någon av de publicerade SDK: er. Media Services-v3 API drivs av Azure Resource Manager, så du kan också använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan användas för att låsa åtkomsten till transformeringar.

Om du behöver uppdatera din [transformera](https://docs.microsoft.com/rest/api/media/transforms), använda den **uppdatera** igen. Den är avsedd för att göra ändringarna för beskrivningen eller prioriteringarna för underliggande TransformOutputs. Vi rekommenderar att dessa uppdateringar ska utföras när alla pågående jobb har slutförts. Om du vill skriva om receptet, måste du skapa en ny omvandling.

### <a name="transform-object-diagram"></a>Omvandla objektet diagram

Följande diagram visar den **transformera** objekt och de objekt som den refererar till, inklusive härledning relationer. Grå pilarna visar en typ som jobbet-referenser och grön pilarna visar härledning klassrelationer.<br/>Klicka på bilden för att visa den i full storlek.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Jobb

En **jobbet** är den faktiska begäran till Azure Media Services att tillämpa den **omvandla** till en given video- eller ljudinnehåll datainnehållet. När vi har skapats, kan du skicka jobb med hjälp av API: er för Media Services eller någon av de publicerade SDK: er. Den **jobbet** anger information som platsen för indatavideo och platsen för utdata. Du kan ange platsen för dina indata video med: HTTPS-adresser, SAS URL: er eller [tillgångar](https://docs.microsoft.com/rest/api/media/assets).  

Använd [jobbet indata från HTTPS](job-input-from-http-how-to.md) om innehållet är redan nås via en URL och du behöver inte lagra källfilen i Azure (till exempel importera från S3). Den här metoden passar också om du har innehållet i Azure Blob storage, men inte har behov av att filen finns i en tillgång. Den här metoden stöder för närvarande bara en enda fil för indata.
 
Använd [tillgången som jobbindata](job-input-from-local-file-how-to.md) om inkommande innehållet är redan i en tillgång eller innehållet lagras i lokal fil. Det är också ett bra alternativ om du planerar att publicera indatatillgången för direktuppspelning eller ladda ned (anta att du vill publicera mp4 för nedladdning men vill också tal till text eller ansikte identifiering). Den här metoden har stöd för flera filtillgångar (till exempel MBR strömmande datauppsättningar som kodats lokalt).
 
Förlopp och status för jobben kan hämtas genom att övervaka händelser med Event Grid. Mer information finns i [övervaka händelser med hjälp av EventGrid](job-state-events-cli-how-to.md).

Uppdateringsåtgärden på den [jobbet](https://docs.microsoft.com/rest/api/media/jobs) entiteten kan användas för att ändra den *beskrivning*, och *prioritet* egenskaper när jobbet har skickats. En ändring av den *prioritet* egenskap gäller endast om jobbet är fortfarande i en kö. Om jobbet har startat bearbetning eller har slutförts, har ändra prioritet ingen effekt.

### <a name="job-object-diagram"></a>Objektet jobbdiagram

Följande diagram visar den **jobbet** objekt och de objekt som den refererar till, inklusive härledning relationer.<br/>Klicka på bilden för att visa den i full storlek.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Konfigurera Mediereserverade enheter

För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 Mediereserverade S3-enheter (MRUs). Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

Mer information finns i [skala mediebearbetning med CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se också

* [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrering, skrivordning, växling av Media Services-entiteter](entities-overview.md)

## <a name="next-steps"></a>Nästa steg

- Innan du börjar utveckla granska [utveckla med API: er för Media Services v3](media-services-apis-overview.md) (innehåller information om hur du använder API: er, namngivningskonventioner, osv.)
- Kolla in de här självstudierna:

    - [Självstudie: Ladda upp, koda och strömma videor med hjälp av .NET](stream-files-tutorial-with-api.md)
    - [Självstudie: Analysera videoklipp med Media Services v3 med hjälp av .NET](analyze-videos-tutorial-with-api.md)
