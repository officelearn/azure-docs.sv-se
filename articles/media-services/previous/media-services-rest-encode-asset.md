---
title: Koda en tillgång som Azure med hjälp av Media Encoder Standard | Microsoft Docs
description: Lär dig hur du använder Media Encoder Standard för att koda medieinnehåll på Azure Media Services. Kodexempel Använd REST API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 34652400acaf2efca3648bb4c7cde795861c3101
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50247937"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Koda en tillgång med Media Encoder Standard
> [!div class="op_single_selector"]
> * [NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Översikt
Om du vill leverera digital video via Internet, måste du komprimera mediet. Digitala videofiler är stora och kan vara för stort för att leverera via Internet eller för kundernas enheter att kunna visas korrekt. Kodning är processen för att komprimera video och ljud så att dina kunder kan visa dina media.

Kodningsjobb är en av de vanligaste bearbetningsuppgifterna i Azure Media Services. Du skapar kodningsjobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda Media Services inbyggda kodaren (Media Encoder Standard). Du kan också använda en kodare som tillhandahålls av Media Services partner. Kodare från tredje part är tillgängliga via Azure Marketplace. Du kan ange information om kodning uppgifter med hjälp av förinställda strängarna som definierats för kodaren eller genom att använda förinställda konfigurationsfiler. Vilka typer av förinställningar som är tillgängliga finns i [uppgift förinställningar för Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning som du vill utföra. Via REST-API kan du skapa jobb och deras relaterade uppgifter i ett av två sätt:

* Aktiviteter kan vara definierats internt via navigeringsegenskapen uppgifter på jobbet entiteter.
* Använd OData-batch-bearbetning.

Vi rekommenderar att du alltid koda källfilerna till en med anpassningsbar bithastighet MP4-uppsättningen och sedan konvertera uppsättningen till det önskade formatet med hjälp av [dynamisk paketering](media-services-dynamic-packaging-overview.md).

Om utdatatillgången är krypterad, måste du konfigurera i principen för tillgångsleverans. Mer information finns i [konfigurera tillgångsleveransprincip](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Överväganden

Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

Innan du börjar refererar till mediebearbetare bör du kontrollera att du har rätt media processor-ID. Mer information finns i [hämta mediebearbetare](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API finns i [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Skapa ett jobb med en enda kodningsjobb
> [!NOTE]
> Följande gäller när du arbetar med Media Services REST-API:
>
> Vid åtkomst till entiteter i Media Services, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
>
> När använder JSON och ange om du vill använda den **__metadata** nyckelord i begäran (till exempel till ett länkat-referens), måste du ställa in den **acceptera** sidhuvud till [JSON utförlig format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Acceptera: application/json; odata = utförlig.
>
>

I följande exempel visas hur du skapar och publicera ett jobb med en aktivitet som anger att koda en video med en viss upplösning och kvalitet. När du kodar med Media Encoder Standard kan du använda aktiviteten configuration förinställningar anges [här](http://msdn.microsoft.com/library/mt269960).

Begäran:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Svar:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Ange namnet på den utdatatillgången
I följande exempel visas hur du ställer in attributet assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Överväganden
* TaskBody egenskaper måste använda literal XML för att ange hur många indata eller utdata tillgångar som används av aktiviteten. Uppgiften artikeln innehåller XML-schemadefinitionen för XML-filen.
* I definitionen av TaskBody varje inre värde för <inputAsset> och <outputAsset> måste anges som JobInputAsset(value) eller JobOutputAsset(value).
* En aktivitet kan ha flera utdataresultat. En JobOutputAsset(x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en indatatillgången för en aktivitet.
* Uppgifter måste inte utgör en cykel.
* Värdeparametern som du skickar till JobInputAsset eller JobOutputAsset representerar index för en tillgång. De faktiska resurser har definierats i navigeringsegenskaper InputMediaAssets och OutputMediaAssets på entiteten jobbdefinitionen.
* Eftersom Media Services bygger på OData v3, enskilda tillgångar i InputMediaAssets och OutputMediaAssets navigering egenskapsuppsättningar refereras via en ”__metadata: uri” namn / värde-par.
* InputMediaAssets mappas till en eller flera resurser som du skapade i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig tillgång.
* OutputMediaAssets kan namnges med hjälp av attributet assetName. Om det här attributet finns inte, så är namnet på OutputMediaAsset är det inre textvärdet för den <outputAsset> elementet är med ett suffix för jobbets namn-värde eller jobb-Id-värdet (i de fall där egenskapen Name inte är definierat). Till exempel om du anger ett värde för assetName till ”exempel” kan anges sedan OutputMediaAsset namnegenskapen till ”exempel”. Men om du inte ange ett värde för assetName, men angetts Jobbnamnet för att ”NewJob”, OutputMediaAsset namnet skulle vara ”JobOutputAsset (värde) _NewJob”.

## <a name="create-a-job-with-chained-tasks"></a>Skapa ett jobb med länkade uppgifter
I många Programscenarier utvecklare som vill skapa en serie bearbetningsåtgärder. Du kan skapa en serie med länkade uppgifter i Media Services. Varje aktivitet utför olika bearbetningssteg och kan använda olika mediebearbetare. Länkade uppgifter kan lämnar in en tillgång från en aktivitet till en annan, utför en linjär aktivitetssekvensen på tillgången. Uppgifter som utförs i ett jobb måste dock inte vara i en sekvens. När du skapar en länkad aktivitet med den länkade **ITask** objekt skapas i en enda **IJob** objekt.

> [!NOTE]
> Det finns en gräns på 30 uppgifter per jobb. Om du vill länka mer än 30 uppgifter kan skapa flera jobb för att innehålla uppgifter.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Överväganden
Aktivera aktiviteten länkning:

* Ett jobb måste ha minst två aktiviteter.
* Det måste finnas minst en aktivitet vars indata är utdata från en annan aktivitet i jobbet.

## <a name="use-odata-batch-processing"></a>Använd OData-batchbearbetning
I följande exempel visar hur du skapar ett jobb och aktiviteter med hjälp av OData-batch-bearbetning. Information om batchbearbetning finns [Open Data Protocol (OData) gruppbearbetning](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Skapa ett jobb med hjälp av en jobbmall
När du bearbetar flera resurser genom att använda en gemensam uppsättning uppgifter kan du använda en jobbmall att ange standard uppgift förinställningar eller att ange ordningen på uppgifter.

I följande exempel visas hur du skapar en jobbmall med en TaskTemplate som har definierats internt. TaskTemplate använder Media Encoder Standard som MediaProcessor för att koda tillgångsfilen. Men kan andra MediaProcessors också användas.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Till skillnad från andra Media Services-entiteter, måste du definiera en ny GUID-identifierare för varje TaskTemplate och placera den i taskTemplateId och Id-egenskap i frågans brödtext. Innehåll identifieringsschema måste följa schemat som beskrivs i identifiera Azure Media Services entiteter. JobTemplates kan inte uppdateras. I stället måste du skapa en ny med ändringarna uppdaterade.
>
>

Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created

    . . .


I följande exempel visas hur du skapar ett jobb som refererar till ett jobbmall-Id:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Om detta lyckas, returneras följande svar:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Avancerad kodning vilka funktioner du vill utforska
* [Generera miniatyrer](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generera miniatyrer vid kodning](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Beskär videor under kodning](media-services-crop-video.md)
* [Anpassa förinställningar för kodning](media-services-custom-mes-presets-with-dotnet.md)
* [Företagsdataskydd eller en vattenstämpel en video med en avbildning](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du skapar ett jobb för att koda en tillgång, se [så kontrollera jobbförloppet för med Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Se också
[Hämta Mediebearbetare](media-services-rest-get-media-processor.md)
