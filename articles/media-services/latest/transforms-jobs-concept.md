---
title: Transformeringar och jobb i Azure Media Services | Microsoft Docs
description: När du använder Media Services måste du skapa en transformering för att beskriva reglerna eller specifikationerna för att bearbeta dina videor. Den här artikeln ger en översikt över vad transformering är och hur du använder den.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637359"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb

Det här avsnittet innehåller information [](https://docs.microsoft.com/rest/api/media/transforms) om transformeringar och [jobb](https://docs.microsoft.com/rest/api/media/jobs) och förklarar förhållandet mellan dessa entiteter. 

## <a name="overview"></a>Översikt 

### <a name="transformsjobs-workflow"></a>Transformeringar/jobb arbets flöde

I följande diagram visas transformeringar/jobb arbets flöde.

![Transformering](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typiskt arbetsflöde

1. Skapa en transformering 
2. Skicka jobb under transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering, om du inte planerar att använda den i framtiden. 

#### <a name="example"></a>Exempel

Anta att du vill extrahera den första bild rutan i alla videor som en miniatyr bild – de steg du skulle vidta är: 

1. Definiera receptet eller regeln för att bearbeta videor – "Använd den första bild rutan i videon som miniatyren". 
2. För varje video ser du till att tjänsten: 
    1. Var du hittar videon,  
    2. Var du vill skriva bilden med miniatyr bilden. 

En **transformering** hjälper dig att skapa ett recept en gång (steg 1) och skicka jobb med det receptet (steg 2).

> [!NOTE]
> Egenskaper för **transformering** och **jobb** som är av typen datetime är alltid i UTC-format.

## <a name="transforms"></a>Transformering

Använd **transformeringar** för att konfigurera vanliga aktiviteter för kodning eller analys av videor. Varje **transformering** beskriver ett recept, eller ett arbets flöde för uppgifter för bearbetning av video-eller ljudfiler. En enda transformering kan använda mer än en regel. En transformering kan till exempel ange att varje video ska kodas till en MP4-fil vid en specifik bit hastighet och att en miniatyr bild genereras från den första bild rutan i videon. Du lägger till en TransformOutput-post för varje regel som du vill inkludera i transformeringen. Du kan använda för inställningar för att berätta för transformeringen hur media-filerna ska bearbetas.

### <a name="viewing-schema"></a>Visa schema

I Media Services v3 är för inställningar starkt skrivna entiteter i själva API: et. Du hittar definitionen "schema" för dessa objekt i [Open API-specifikationen (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också Visa förinställda definitioner (som **StandardEncoderPreset**) i [REST API](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset), [.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (eller andra Media Services v3 SDK referens dokumentation).

### <a name="creating-transforms"></a>Skapar transformeringar

Du kan skapa transformeringar med hjälp av REST, CLI eller använda någon av de publicerade SDK: erna. Media Services v3-API: et styrs av Azure Resource Manager, så du kan även använda Resource Manager-mallar för att skapa och distribuera transformeringar i ditt Media Services-konto. Rollbaserad åtkomst kontroll kan användas för att låsa åtkomsten till transformeringar.

### <a name="updating-transforms"></a>Uppdaterar transformeringar

Om du behöver uppdatera transformeringen [](https://docs.microsoft.com/rest/api/media/transforms)använder du uppdaterings åtgärden. Den är avsedd för att göra ändringar i beskrivningen eller prioriteterna för underliggande TransformOutputs. Vi rekommenderar att sådana uppdateringar utförs när alla pågående jobb har slutförts. Om du tänker skriva om receptet måste du skapa en ny transformering.

### <a name="transform-object-diagram"></a>Transformera objekt diagram

Följande diagram visar **Transform** -objektet och de objekt som det refererar till, inklusive härlednings relationer. Grå pilarna visar en typ som jobbet refererar till och gröna pilar visar klass härlednings relationer.<br/>Klicka på bilden för att visa den i full storlek.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Jobb

Ett **jobb** är den faktiska begäran om Azure Media Services att tillämpa **transformeringen** på ett angivet video-eller ljud innehåll. När transformeringen har skapats kan du skicka jobb med Media Services-API: er eller någon av de publicerade SDK: erna. **Jobbet** anger information som till exempel platsen för indata-videon och platsen för utdata. Du kan ange platsen för Indataporten med: HTTPS-URL: er, SAS-URL: er eller [till gångar](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Jobb inmatat från HTTPS

Använd [jobb indata från https](job-input-from-http-how-to.md) om ditt innehåll redan är tillgängligt via en URL och du inte behöver lagra käll filen i Azure (till exempel importera från S3). Den här metoden är också lämplig om du har innehållet i Azure Blob Storage men inte behöver filen i en till gång. För närvarande stöder den här metoden bara en enda fil för indata.

### <a name="asset-as-job-input"></a>Till gång som jobb inmatat

Använd [till gång som indata för jobb](job-input-from-local-file-how-to.md) om indata-innehållet redan finns i en till gång eller om innehållet lagras i en lokal fil. Det är också ett bra alternativ om du planerar att publicera indata till gången för strömning eller hämtning (anta att du vill publicera MP4 för nedladdning, men även vill göra tal till text eller ansikts igenkänning). Den här metoden stöder flersidiga till gångar (t. ex. MBR streaming-uppsättningar som kodats lokalt).

### <a name="checking-job-progress"></a>Kontrollerar jobb förlopp

Jobbets förlopp och tillstånd kan hämtas genom att övervaka händelser med Event Grid. Mer information finns i [övervaka händelser med hjälp av EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Uppdaterar jobb

Uppdaterings åtgärden för [jobb](https://docs.microsoft.com/rest/api/media/jobs) -entiteten kan användas för att ändra *beskrivningen*och prioritets egenskaperna när jobbet har skickats. En ändring av *prioritets* egenskapen är endast effektiv om jobbet fortfarande har statusen i kö. Om jobbet har påbörjat bearbetningen eller har avslut ATS har ändrings prioriteten ingen påverkan.

### <a name="job-object-diagram"></a>Jobb objekts diagram

Följande diagram visar **jobbobjektet** och de objekt som det refererar till, inklusive härlednings relationer.<br/>Klicka på bilden för att visa den i full storlek.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Konfigurera reserverade enheter för media

För ljud analys-och video analys jobb som utlöses av Media Services v3 eller Video Indexer, rekommenderar vi starkt att du etablerar ditt konto med 10 MRUs (S3 Media reservered units). Om du behöver fler än 10 S3 MRUs kan öppna en stöd biljett med den [Azure-portalen](https://portal.azure.com/).

Mer information finns i [skala medie bearbetning med CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se också

* [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md)

## <a name="next-steps"></a>Nästa steg

- Innan du börjar utveckla bör du läsa [utveckla med Media Services v3-API: er](media-services-apis-overview.md) (innehåller information om hur du kommer åt API: er, namngivnings konventioner osv.)
- Kolla i de här självstudierna:

    - [Självstudier: Koda en fjärrfil utifrån URL och strömma videon](stream-files-tutorial-with-rest.md)
    - [Självstudier: Ladda upp, koda och strömma videor](stream-files-tutorial-with-api.md)
    - [Självstudier: Analysera videor med Media Services v3](analyze-videos-tutorial-with-api.md)
