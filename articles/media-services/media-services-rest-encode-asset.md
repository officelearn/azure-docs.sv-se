---
title: "Koda en Azure-tillgång med Media Encoder Standard | Microsoft Docs"
description: "Lär dig hur du använder Media Encoder Standard för att koda medieinnehåll på Azure Media Services. Kodexempel använda REST API."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: juliako
ms.openlocfilehash: 796f3b5a4dd56a0160986600cbbcf38faf8add56
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Koda en tillgång med Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Översikt
Om du vill leverera digital video via Internet, måste du komprimera mediet. Digitala videofiler är stort och kan vara för stort för att leverera via Internet eller för dina kunders enheter att kunna visas korrekt. Kodning är en process för att komprimera video och ljud så att kunderna kan visa dina media.

Kodning jobb är en av de vanligaste bearbetningsprocessen i Azure Media Services. Du skapar kodningsjobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda Media Services inbyggda kodaren (Media Encoder Standard). Du kan också använda en kodare som tillhandahålls av Media Services partner. Kodare från tredje part är tillgängliga via Azure Marketplace. Du kan ange information om kodning uppgifter med hjälp av förinställda strängar som definierats för din kodare eller med hjälp av förinställda konfigurationsfiler. Om du vill se vilka typer av förinställningar som är tillgängliga finns [aktivitet förinställningar för Media Encoder Standard](http://msdn.microsoft.com/library/mt269960).

Varje jobb kan ha en eller flera aktiviteter beroende på vilken typ av bearbetning som du vill utföra. Via REST-API kan du skapa jobb och deras relaterade uppgifter i ett av två sätt:

* Aktiviteter kan vara definierade infogade via navigeringsegenskap uppgifter på jobbet entiteter.
* Använd OData-batch-bearbetning.

Vi rekommenderar att du alltid koda källfilerna till en MP4-uppsättningen med anpassad bithastighet och sedan konvertera uppsättningen önskat format med hjälp av [dynamisk paketering](media-services-dynamic-packaging-overview.md).

Om din utdatatillgången är lagringskrypterad, måste du konfigurera i principen för tillgångsleverans. Mer information finns i [konfigurera tillgångsleveransprincip](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Överväganden

Vid åtkomst till entiteter i Media Services måste du ange specifika namn på huvudfält och värden i HTTP-begäranden. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).

Innan du börjar refererar till media processorer, kontrollera att du har rätt media processor-ID. Mer information finns i [hämta medieprocessorer](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS API: et finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>När du har anslutit till https://media.windows.net, får du en 301 omdirigering att ange en annan Media Services-URI. Du måste göra följande anrop till en ny URI.

## <a name="create-a-job-with-a-single-encoding-task"></a>Skapa ett jobb med en enda kodning aktivitet
> [!NOTE]
> Följande gäller när du arbetar med Media Services REST API:
>
> Vid åtkomst till entiteter i Media Services måste du ange specifika namn på huvudfält och värden i HTTP-begäranden. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md).
>
> När du har anslutit till https://media.windows.net, får du en 301 omdirigering att ange en annan Media Services-URI. Du måste göra följande anrop till en ny URI. Information om hur du ansluter till AMS API: et finns [åtkomst till Azure Media Services-API med Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md).
>
> När med JSON och ange om du vill använda den **__metadata** nyckelord i begäran (till exempel att refererar till ett länkat), måste du ange den **acceptera** sidhuvud till [utförlig JSON-format](http://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): acceptera: application/json; odata = utförlig.
>
>

I följande exempel visas hur du skapar och efter ett jobb med en aktivitet som anger att koda ett videoklipp vid en viss upplösning och kvalitet. När du kodar med Media Encoder Standard kan du använda aktiviteten configuration förinställningar anges [här](http://msdn.microsoft.com/library/mt269960).

Begäran:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Svar:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Ange namnet på utdatatillgången
I följande exempel visas hur du ställer in attributet assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Överväganden
* TaskBody egenskaper måste använda literal XML för att definiera antalet indata eller utdata tillgångar som används av aktiviteten. Aktiviteten innehåller XML-schemadefinitionen för XML-filen.
* I definitionen TaskBody varje inre värde för <inputAsset> och <outputAsset> måste anges som JobInputAsset(value) eller JobOutputAsset(value).
* En aktivitet kan innehålla flera utdata tillgångar. En JobOutputAsset(x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som inkommande tillgång för en aktivitet.
* Aktiviteter måste inte utgör en cykel.
* Värdeparametern som du skickar till JobInputAsset eller JobOutputAsset representerar indexet för en tillgång. De faktiska tillgångarna har definierats i navigeringsegenskaper InputMediaAssets och OutputMediaAssets på entiteten jobbdefinitionen.
* Eftersom Media Services bygger på OData v3 enskilda tillgångar i InputMediaAssets och OutputMediaAssets navigering egenskapsuppsättningar refereras via en ”__metadata: uri” namn / värde-par.
* InputMediaAssets mappas till en eller flera resurser som du skapade i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig tillgång.
* OutputMediaAssets kan namnges med hjälp av attributet assetName. Om det här attributet är inte tillgängligt och sedan namnet på OutputMediaAsset är det inre textvärdet för den <outputAsset> elementet är med suffixet namnvärdet för jobb eller jobb-Id-värde (i de fall där egenskapen namn har inte definierats). Till exempel om du anger ett värde för assetName till ”Sample”, anges sedan OutputMediaAsset Name-egenskapen till ”Sample”. Men om du inte ange ett värde för assetName, men det angetts på jobbnamnet till ”NewJob”, OutputMediaAsset namnet skulle vara ”JobOutputAsset (värde) _NewJob”.

## <a name="create-a-job-with-chained-tasks"></a>Skapa ett jobb med länkad uppgifter
I många Programscenarier utvecklare som vill skapa en serie av uppgifter. Du kan skapa en serie länkad aktiviteter i Media Services. Varje aktivitet utför steg för behandling och kan använda olika media processorer. Länkad uppgifter kan lämnar in en tillgång från en aktivitet till en annan, utför en linjär aktivitetssekvensen på tillgången. Uppgifter som utförs i ett jobb krävs dock inte att vara i en sekvens. När du skapar en länkad aktivitet i länkad **ITask** objekt som skapas i en enda **IJob** objekt.

> [!NOTE]
> Det finns en gräns på 30 uppgifter per jobb. Om du behöver mer än 30 uppgifter knutna skapa mer än ett jobb för att innehålla aktiviteter.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
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
Så här aktiverar du länkning av aktivitet:

* Ett jobb måste ha minst två aktiviteter.
* Det måste finnas minst en aktivitet vars indata är resultatet av en annan aktivitet i jobbet.

## <a name="use-odata-batch-processing"></a>Använd OData-batch-bearbetning
I följande exempel visas hur du skapar ett jobb och aktiviteter med hjälp av OData-batch-bearbetning. Mer information om batchbearbetning finns [Open Data Protocol (OData) gruppbearbetning](http://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <token>
    x-ms-version: 2.11
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
    Authorization: Bearer <token>
    x-ms-version: 2.11
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
    Authorization: Bearer <token>
    x-ms-version: 2.11
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Skapa ett jobb med hjälp av en jobbmall
När du bearbeta flera resurser med hjälp av en gemensam uppsättning med uppgifter kan du använda en jobbmall Ange standard uppgiften förinställningar eller för att ange uppgifter.

I följande exempel visas hur du skapar en jobbmall med en TaskTemplate som är definierade infogad. TaskTemplate använder Media Encoder Standard som MediaProcessor för att koda resursfilen. Dock kan andra MediaProcessors också användas.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Till skillnad från andra Media Services-enheter måste du definiera en ny GUID-identifierare för varje TaskTemplate och placera det i taskTemplateId och Id-egenskap i frågans brödtext. Innehåll identifieringsschemat måste följa det schema som beskrivs i identifiera Azure Media Services entiteter. JobTemplates kan inte uppdateras. I stället måste du skapa en ny med ändringarna uppdaterade.
>
>

Om det lyckas, returneras följande svar:

    HTTP/1.1 201 Created

    . . .


I följande exempel visas hur du skapar ett jobb som refererar till en jobbmall-Id:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.11
    Authorization: Bearer <token value>
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Om det lyckas, returneras följande svar:

    HTTP/1.1 201 Created

    . . .



## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du skapar ett jobb för att koda en tillgång, se [hur du kan följa jobbförloppet med Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Se även
[Hämta Media-processorer](media-services-rest-get-media-processor.md)
