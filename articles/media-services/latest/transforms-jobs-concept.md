---
title: Transformeringar och jobb i medietjänster
titleSuffix: Azure Media Services
description: Lär dig hur du skapar en transformeringar för att beskriva reglerna för bearbetning av dina videor i Azure Media Services.
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
ms.openlocfilehash: ab99b974aed6f8cd5e1da2ee9b427f593b405889
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73571240"
---
# <a name="transforms-and-jobs-in-media-services"></a>Transformeringar och jobb i medietjänster

Det här avsnittet innehåller information om [transformeringar](https://docs.microsoft.com/rest/api/media/transforms) och [jobb](https://docs.microsoft.com/rest/api/media/jobs) och förklarar relationen mellan dessa entiteter.

## <a name="overview"></a>Översikt

### <a name="transformsjobs-workflow"></a>Transformeringar/jobbarbetsflöde

I följande diagram visas arbetsflödet för transformeringar/jobb:

![Transformeringar och jobbarbetsflöde i Azure Media Services](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Typiskt arbetsflöde

1. Skapa en transformering.
2. Skicka jobb under den transformeringen.
3. Lista transformeringar.
4. Ta bort en transformering om du inte planerar att använda den i framtiden.

#### <a name="example"></a>Exempel

Anta att du ville extrahera den första bildrutan i alla dina videor som en miniatyrbild– de steg du skulle ta är:

1. Definiera receptet, eller regeln för bearbetning av dina videor: "använd den första bildrutan i videon som miniatyr".
2. För varje video talar du om för tjänsten:
    1. Var hittar du videon.
    2. Var du ska skriva utdataminiatyrbilden.

En **transformering** hjälper dig att skapa receptet en gång (steg 1) och skicka jobb med det receptet (steg 2).

> [!NOTE]
> Egenskaper för **Transform och** **Job** för typen Datetime är alltid i UTC-format.

## <a name="transforms"></a>Transformering

Använd **Transformeringar** för att konfigurera vanliga uppgifter för kodning eller analys av videor. Varje **transformering** beskriver ett recept eller ett arbetsflöde med uppgifter för bearbetning av video- eller ljudfiler. En enda transformering kan tillämpa mer än en regel. En transformering kan till exempel ange att varje video ska kodas till en MP4-fil med en viss bithastighet och att en miniatyrbild genereras från den första bildrutan i videon. Du lägger till en TransformOutput-post för varje regel som du vill inkludera i transformeringen. Du använder förinställningar för att tala om för transformeringen hur indatamediefilerna ska bearbetas.

### <a name="viewing-schema"></a>Visa schema

I Media Services v3 skrivs förinställningar starkt till att entiteterna i själva API:et skrivs. Du hittar "schema"-definitionen för dessa objekt i [Open API Specification (eller Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). Du kan också visa de förinställda definitionerna (till exempel **StandardEncoderPreset)** i [REST API,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet)eller annan Media Services v3 SDK-referensdokumentation.

### <a name="creating-transforms"></a>Skapa transformeringar

Du kan skapa transformeringar med REST, CLI eller någon av de publicerade SDK:erna. Api:et för Media Services v3 drivs av Azure Resource Manager, så du kan också använda Resource Manager-mallar för att skapa och distribuera transformeringar i ditt Media Services-konto. Rollbaserad åtkomstkontroll kan användas för att låsa åtkomsten till Transforms.

### <a name="updating-transforms"></a>Uppdatera transformeringar

Om du behöver uppdatera [transformeringen](https://docs.microsoft.com/rest/api/media/transforms)använder du åtgärden **Uppdatera.** Den är avsedd för att göra ändringar i beskrivningen eller prioriteringarna för de underliggande TransformOutputs. Vi rekommenderar att sådana uppdateringar görs när alla pågående jobb har slutförts. Om du tänker skriva om receptet måste du skapa en ny transformering.

### <a name="transform-object-diagram"></a>Omforma objektdiagram

I följande diagram visas **omformningsobjektet** och de objekt som det refererar till, inklusive härledningsrelationerna. De grå pilarna visar en typ som jobbreferenserna och de gröna pilarna visar klassderingsrelationer.

Markera bilden om du vill visa den i full storlek.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a>

## <a name="jobs"></a>Jobb

Ett **jobb** är den faktiska begäran till Media Services att tillämpa **Transform** på ett visst indatavideo- eller ljudinnehåll. När transformeringen har skapats kan du skicka jobb med Api:er för Media Services eller någon av de publicerade SDK:erna. **Jobbet** anger information som platsen för indatavideon och platsen för utdata. Du kan ange platsen för indatavideon med hjälp av HTTPS-URL:er, SAS-url:er eller [Tillgångar](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Jobbindata från HTTPS

Använd [jobbindata från HTTPS](job-input-from-http-how-to.md) om ditt innehåll redan är tillgängligt via en URL och du inte behöver lagra källfilen i Azure (till exempel importera från S3). Den här metoden är också lämplig om du har innehållet i Azure Blob-lagring men inte behöver filen vara i en tillgång. För närvarande stöder den här metoden bara en enda fil för indata.

### <a name="asset-as-job-input"></a>Tillgång som jobbindata

Använd [Tillgång som jobbinmatning](job-input-from-local-file-how-to.md) om indatainnehållet redan finns i en tillgång eller om innehållet lagras i lokal fil. Det är också ett bra alternativ om du planerar att publicera indata tillgången för streaming eller nedladdning (säger att du vill publicera mp4 för nedladdning men också vill göra tal till text eller ansiktsigenkänning). Den här metoden stöder flerfilstillgångar (till exempel MBR-direktuppspelningsuppsättningar som kodades lokalt).

### <a name="checking-job-progress"></a>Kontrollera jobbstatus

Förlopp och tillstånd för jobb kan erhållas genom att övervaka händelser med Event Grid. Mer information finns i [Övervaka händelser med EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Uppdatera jobb

Uppdateringsåtgärden på [jobbentiteten](https://docs.microsoft.com/rest/api/media/jobs) kan användas för att ändra *beskrivningen* och *prioritetsegenskaperna* när jobbet har skickats. En ändring *priority* av prioritetsegenskapen gäller endast om jobbet fortfarande är i kö. Om jobbet har börjat bearbeta, eller har slutförts, har ändring av prioritet ingen effekt.

### <a name="job-object-diagram"></a>Diagram över jobbobjekt

I följande diagram visas **projektobjektet** och de objekt som det refererar till, inklusive härledningsrelationerna.

Klicka på bilden för att visa den i full storlek.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a>

## <a name="configure-media-reserved-units"></a>Konfigurera reserverade medieenheter

För jobb för ljudanalys och videoanalys som utlöses av Media Services v3 eller Video Indexer rekommenderar vi starkt att ditt konto får 10 S3-mediereserverade enheter (MRUs). Om du behöver mer än 10 S3 MRUs öppnar du en supportbiljett med [Azure-portalen](https://portal.azure.com/).

Mer information finns i [Skala mediebearbetning med CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="see-also"></a>Se även

* [Felkoder](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrering, beställning, växling av Medietjänster](entities-overview.md)

## <a name="next-steps"></a>Nästa steg

- Innan du börjar utveckla, granska [Utveckla med Media Services v3 API:er](media-services-apis-overview.md) (innehåller information om åtkomst till API:er, namngivningskonventioner osv.)
- Kolla in dessa tutorials:

    - [Självstudiekurs: Koda en fjärrfil baserat på URL och strömma videon](stream-files-tutorial-with-rest.md)
    - [Självstudiekurs: Ladda upp, koda och strömma videor](stream-files-tutorial-with-api.md)
    - [Självstudiekurs: Analysera videor med Media Services v3](analyze-videos-tutorial-with-api.md)
