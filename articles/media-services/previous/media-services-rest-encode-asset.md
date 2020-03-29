---
title: Så här kodar du en Azure-tillgång med hjälp av Media Encoder Standard | Microsoft-dokument
description: Lär dig hur du använder Media Encoder Standard för att koda medieinnehåll på Azure Media Services. Kodexempel använder REST API.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 6854400f2152a5952a7b24dbd860d7ad4bfc943d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774924"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Så här kodar du en tillgång med hjälp av Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [Resten](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Översikt

Om du vill leverera digital video via Internet måste du komprimera mediet. Digitala videofiler är stora och kan vara för stora för att levereras via Internet, eller för att kundernas enheter ska visas korrekt. Kodning är processen att komprimera video och ljud så att dina kunder kan visa dina media.

Kodningsjobb är en av de vanligaste bearbetningsåtgärderna i Azure Media Services. Du skapar kodningsjobb för att konvertera mediefiler från en kodning till en annan. När du kodar kan du använda den inbyggda kodaren mediatjänster (Media Encoder Standard). Du kan också använda en kodare som tillhandahålls av en Media Services-partner. Kodare från tredje part är tillgängliga via Azure Marketplace. Du kan ange information om kodningsuppgifter med hjälp av förinställda strängar som definierats för kodaren eller med hjälp av förinställda konfigurationsfiler. Information om vilka typer av förinställningar som är tillgängliga finns i [Aktivitetsförinställningar för Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Varje jobb kan ha en eller flera uppgifter beroende på vilken typ av bearbetning du vill utföra. Genom REST API kan du skapa jobb och deras relaterade uppgifter på ett av två sätt:

* Aktiviteter kan definieras infogade via egenskapen Uppgiftervigering på jobbentiteter.
* Använd OData-batchbearbetning.

Vi rekommenderar att du alltid kodar källfilerna till en adaptiv bithastighet MP4-uppsättning och sedan konverterar uppsättningen till önskat format med hjälp av [dynamisk förpackning](media-services-dynamic-packaging-overview.md).

Om utdatatillgången är lagringskrypterad måste du konfigurera principen för tillgångsleverans. Mer information finns i [Konfigurera leveransprincip för tillgångar](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Överväganden

När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md).

Innan du börjar referera till medieprocessorer kontrollerar du att du har rätt medieprocessor-ID. Mer information finns i [Hämta medieprocessorer](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

Information om hur du ansluter till AMS-API:et finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Skapa ett jobb med en enda kodningsuppgift

> [!NOTE]
> När du arbetar med REST-API:et för Medietjänster gäller följande:
>
> När du öppnar entiteter i Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API-utveckling för Media Services](media-services-rest-how-to-use.md).
>
> När du använder JSON och anger att nyckelordet **__metadata** används i begäran (till exempel för att referera till ett länkat objekt) måste du ange **formatet Acceptera** rubrik till [JSON Verbose](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Acceptera: application/json;odata=verbose.
>
>

I följande exempel visas hur du skapar och bokför ett jobb med en aktivitet inställd på att koda en video med en viss upplösning och kvalitet. När du kodar med Media Encoder Standard kan du använda förinställningar för aktivitetskonfiguration som anges [här](https://msdn.microsoft.com/library/mt269960).

Begäran:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Svar:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Ange utdatatillgångens namn
I följande exempel visas hur du ställer in attributet assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Överväganden
* TaskBody-egenskaper måste använda litteral XML för att definiera antalet indata eller utdatatillgångar som används av aktiviteten. The task article contains the XML Schema Definition for the XML.
* I TaskBody-definitionen måste varje `<inputAsset>` `<outputAsset>` inre värde för och ställas in som JobInputAsset(value) eller JobOutputAsset(värde).
* En aktivitet kan ha flera utdatatillgångar. En JobOutputAsset(x) kan bara användas en gång som utdata för en aktivitet i ett jobb.
* Du kan ange JobInputAsset eller JobOutputAsset som en indatatillgång för en aktivitet.
* Aktiviteter får inte utgöra en cykel.
* Värdeparametern som du skickar till JobInputAsset eller JobOutputAsset representerar indexvärdet för en tillgång. De faktiska tillgångarna definieras i navigeringsegenskaperna InputMediaAssets och OutputMediaAssets på jobbentitetsdefinitionen.
* Eftersom Media Services bygger på OData v3 refereras de enskilda tillgångarna i navigeringsegenskapssamlingarna InputMediaAssets och OutputMediaAssets via ett namnvärdespar för "__metadata: uri".
* InputMediaAssets mappar till en eller flera tillgångar som du har skapat i Media Services. OutputMediaAssets skapas av systemet. De refererar inte till en befintlig tillgång.
* OutputMediaAssets kan namnges med attributet assetName. Om det här attributet inte finns är namnet på OutputMediaAsset `<outputAsset>` det inre textvärdet för elementet som har ett suffix med värdet Jobbnamn eller värdet Jobb-ID (i det fall där egenskapen Namn inte har definierats). Om du till exempel anger ett värde för assetName till "Exempel" anges egenskapen OutputMediaAsset Name till "Exempel". Men om du inte angav ett värde för assetName, men angav jobbnamnet till "NewJob", blir namnet För OutputMediaAsset "JobOutputAsset(value)_NewJob."

## <a name="create-a-job-with-chained-tasks"></a>Skapa ett jobb med kedjade uppgifter
I många programscenarier vill utvecklare skapa en serie bearbetningsuppgifter. I Media Services kan du skapa en serie kedjade uppgifter. Varje uppgift utför olika bearbetningssteg och kan använda olika medieprocessorer. De kedjade aktiviteterna kan lämna över en tillgång från en aktivitet till en annan och utföra en linjär sekvens av aktiviteter på tillgången. De uppgifter som utförs i ett jobb behöver dock inte vara i en sekvens. När du skapar en kedjad uppgift skapas de kedjade **ITask-objekten** i ett enda **IJob-objekt.**

> [!NOTE]
> Det finns för närvarande en gräns på 30 aktiviteter per jobb. Om du behöver kedja fler än 30 aktiviteter skapar du mer än ett jobb som innehåller aktiviteterna.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
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
Så här aktiverar du aktivitetskedja:

* Ett jobb måste ha minst två aktiviteter.
* Det måste finnas minst en aktivitet vars indata är utdata för en annan aktivitet i projektet.

## <a name="use-odata-batch-processing"></a>Använda OData-batchbearbetning
I följande exempel visas hur du använder OData-batchbearbetning för att skapa ett jobb och uppgifter. Information om batchbearbetning finns i [OData-batchbearbetning (Open Data Protocol).](https://www.odata.org/documentation/odata-version-3-0/batch-processing/)

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.19
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
    x-ms-version: 2.19
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
    x-ms-version: 2.19
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Skapa ett jobb med hjälp av en JobTemplate
När du bearbetar flera resurser med hjälp av en gemensam uppsättning aktiviteter använder du en JobTemplate för att ange standardaktivitetsförinställningar eller för att ange aktivitetsordning.

I följande exempel visas hur du skapar en JobTemplate med en TaskTemplate som definieras infogad. I TaskTemplate används Media Encoder Standard som MediaProcessor för att koda tillgångsfilen. Andra MediaProcessors kan dock också användas.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Till skillnad från andra Media Services-entiteter måste du definiera en ny GUID-identifierare för varje TaskTemplate och placera den i egenskapen taskTemplateId och Id i din begärandetext. Innehållsidentifieringsschemat måste följa schemat som beskrivs i Identifiera Azure Media Services-entiteter. JobTemplates kan inte heller uppdateras. I stället måste du skapa en ny med dina uppdaterade ändringar.
>
>

Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created

    . . .


I följande exempel visas hur du skapar ett jobb som refererar till ett JobTemplate-ID:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Om det lyckas returneras följande svar:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Avancerade kodningsfunktioner för att utforska
* [Så här skapar du miniatyrer](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Generera miniatyrer under kodning](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Beskära videor under kodning](media-services-crop-video.md)
* [Anpassa kodningsförinställningar](media-services-custom-mes-presets-with-dotnet.md)
* [Överlägg eller vattenstämpel en video med en bild](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Nästa steg
Nu när du vet hur du skapar ett jobb för att koda en tillgång läser du [Så här kontrollerar du jobbförloppet med Media Services](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Se även
[Hämta medieprocessorer](media-services-rest-get-media-processor.md)
