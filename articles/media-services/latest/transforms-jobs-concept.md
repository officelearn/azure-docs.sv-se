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
ms.date: 12/20/2018
ms.author: juliako
ms.openlocfilehash: 95079813cf3ade41d17393168116e4767ca26e99
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742787"
---
# <a name="transforms-and-jobs"></a>Transformeringar och jobb
 
Använd [omvandlar](https://docs.microsoft.com/rest/api/media/transforms) att konfigurera vanliga uppgifter för kodning eller analysera videor. Varje **transformera** beskriver ett recept eller ett arbetsflöde med uppgifter för att bearbeta video- eller ljudinnehåll filer. 

En [jobbet](https://docs.microsoft.com/rest/api/media/jobs) är den faktiska begäran till Azure Media Services att tillämpa den **omvandla** till en given video- eller ljudinnehåll datainnehållet. Den **jobbet** anger information som platsen för indatavideo och platsen för utdata. Du kan ange platsen för dina indata video med: HTTPS-adresser, SAS URL: er eller [Media Services-tillgångar](https://docs.microsoft.com/rest/api/media/assets).  

## <a name="typical-workflow"></a>Typiskt arbetsflöde

1. Skapa en transformering 
2. Skicka jobb under den transformeringen 
3. Lista transformeringar 
4. Ta bort en transformering, om du inte planerar att använda den i framtiden. 

Anta att du vill extrahera första bildruta från dina videor som en miniatyrbild – är vad du måste göra: 

1. Definiera receptet eller regeln för att bearbeta dina videor – ”Använd första ramen videoklippets som miniatyren”. 
2. För varje video skulle du instruera tjänsten: 
    1. Var du hittar den här videon  
    2. Var ska skriva på miniatyrbilden för utdata. 

En **transformera** hjälper dig att skapa receptet en gång (steg 1) och skicka jobb med hjälp av det receptet (steg 2).

## <a name="transforms"></a>Transformering

Du kan skapa transformeringar i Media Services-kontot med v3-API: et direkt eller med någon av de publicerade SDK: er. Media Services-v3 API drivs av Azure Resource Manager, så du kan också använda Resource Manager-mallar för att skapa och distribuera transformeringar i Media Services-kontot. Rollbaserad åtkomstkontroll kan användas för att låsa åtkomsten till transformeringar.

### <a name="transform-definition"></a>Omvandla definition

I följande tabell visar den transformering egenskaper och ger definitionerna.

|Namn|Beskrivning|
|---|---|
|Id|Fullständigt kvalificerade resurs-ID för resursen.|
|namn|Namnet på resursen.|
|Properties.Created |UTC-datum och tid när transformering skapades i ”åååå-MM-: ssZ ' format.|
|properties.description |En valfri utförlig beskrivning av transformeringen.|
|properties.lastModified |UTC-datum och tid när vi senast uppdaterades, i ”åååå-MM-: ssZ ' format.|
|Properties.outputs |En matris med en eller flera TransformOutputs som vi ska skapa.|
|typ|Typ av resursen.|

Läs den fullständiga definitionen [omvandlar](https://docs.microsoft.com/rest/api/media/transforms).

Enligt beskrivningen ovan, kan du skapa en recept eller en regel för att bearbeta dina videor i en transformering. En enda transformering kan använda mer än en regel. En transformering kan till exempel ange att varje videon kodas till en MP4-fil på en viss bithastighet och att en miniatyrbild genereras från den första bildrutan i videon. Du skulle lägga till en TransformOutput post för varje regel som du vill ska ingå i din transformeringen.

> [!NOTE]
> Medan transformeringar definition stöder en uppdateringsåtgärd, det är viktigt för att kontrollera att alla pågående jobb slutförts innan du gör en ändring. Du skulle normalt uppdatera en befintlig transformeringen om du vill ändra beskrivningen eller ändra prioriteringarna för underliggande TransformOutputs. Om du vill skriva om receptet skulle du skapa en ny omvandling.

## <a name="jobs"></a>Jobb

Du kan skicka jobb med hjälp av API: er för Media Services eller någon av de publicerade SDK: er när du har skapat en transformering. Förlopp och status för jobben kan hämtas genom att övervaka händelser med Event Grid. Mer information finns i [övervaka händelser med hjälp av EventGrid](job-state-events-cli-how-to.md ).

### <a name="jobs-definition"></a>Definition av jobb

I följande tabell visar jobbs egenskaper och ger definitionerna.

|Namn|Beskrivning|
|---|---|
|id|Fullständigt kvalificerade resurs-ID för resursen.|
|namn   |Namnet på resursen.|
|properties.correlationData |Kunden tillhandahålla (kan ändras) returnerade korrelationsdata som en del av jobbet och JobOutput ändringsmeddelanden. Mer information finns i [Händelsescheman](media-services-event-schemas.md)<br/><br/>Egenskapen kan också användas för användning med flera innehavare för Media Services. Du kan placera klient-ID: N i jobb. |
|Properties.Created |UTC-datum och tid då jobbet skapades i ”åååå-MM-: ssZ ' format.|
|properties.description |Valfritt kunden Anger beskrivningen av jobbet.|
|Properties.Input|Indata för jobbet.|
|properties.lastModified    |UTC-datum och tid då jobbet senast uppdaterades, i ”åååå-MM-: ssZ ' format.|
|Properties.outputs|Utdata för jobbet.|
|Properties.Priority    |Prioritet som jobbet ska bearbetas. Jobb med högre prioritet bearbetas före jobb med lägre prioritet. Om inte är aktiverad, standardvärdet är normal.|
|Properties.state   |Det aktuella tillståndet för jobbet.|
|typ   |Typ av resursen.|

Läs den fullständiga definitionen [jobb](https://docs.microsoft.com/rest/api/media/jobs).

> [!NOTE]
> När du jobb definition stöder en uppdateringsåtgärd, är de enda egenskaper som kan ändras när jobbet har skickats beskrivningen och prioritet. Dessutom kan gäller en ändring av prioritet till och endast om jobbet är fortfarande i en kö. Om jobbet har startat bearbetning eller har slutförts, har ändra prioriteten ingen effekt.

### <a name="pagination"></a>Sidbrytning

Jobb sidbrytning stöds i Media Services v3.

> [!TIP]
> Du bör alltid använda nästa länk för att räkna upp samlingen och inte är beroende av en viss storlek.

Om ett frågesvar innehåller många objekt, tjänsten returnerar en ”\@odata.nextLink” egenskapen för att hämta nästa sida i resultatet. Detta kan användas för att bläddra igenom hela resultatmängden. Du kan inte konfigurera sidstorleken. 

Om jobb skapas eller tas bort vid bläddring genom samlingen, syns ändringarna i de returnerade resultaten (om dessa ändringar finns i en del av den samling som inte har hämtats.) 

Följande C# exempel visas hur du räknar upp via jobb i kontot.

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST-exempel finns i [jobb – lista](https://docs.microsoft.com/rest/api/media/jobs/list)


## <a name="next-steps"></a>Nästa steg

[Stream-filer](stream-files-dotnet-quickstart.md)
