---
title: Överföra filer till ett Azure Media Services-konto med hjälp av REST | Microsoft Docs
description: Lär dig hur du hämtar medieinnehåll till Media Services genom att skapa och ladda upp tillgångar.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 23650a3ad4bb9e1e00c07ec0312b02952a9a12ac
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2019
ms.locfileid: "55981238"
---
# <a name="upload-files-into-a-media-services-account-using-rest-legacy"></a>Överföra filer till ett Media Services-konto med hjälp av REST (äldre)
> [!div class="op_single_selector"]
> * [NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

I Media Services överför du dina digitala filer till en tillgång. Den [tillgången](https://docs.microsoft.com/rest/api/media/operations/asset) entiteten kan innehålla video, ljud, bilder, miniatyrsamlingar, textspår och dold textning filer (och metadata om dessa filer.)  När filerna har överförts till tillgången, lagras innehållet på ett säkert sätt i molnet för ytterligare bearbetning och strömning. 

Lär dig hur du överför en fil och andra åtgärder som är kopplade till den i den här självstudien:

> [!div class="checklist"]
> * Konfigurera Postman för alla åtgärder för uppladdning
> * Ansluta till Media Services 
> * Skapa en åtkomstprincip med behörighet att skriva
> * Skapa en tillgång
> * Skapa en SAS-lokaliserare och skapa URL
> * Överföra en fil till blob storage med hjälp av URL
> * Skapa en metadata i tillgången för mediefilen som du laddat upp

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Skapa ett Azure Media Services-konto med Azure portal](media-services-portal-create-account.md).
- Granska den [åtkomst till Azure Media Services-API med AAD-autentisering översikt](media-services-use-aad-auth-to-access-ams-api.md) artikeln.
- Konfigurera **Postman** enligt beskrivningen i [konfigurera Postman för Media Services REST API-anrop](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Överväganden

Följande gäller när du använder Media Services REST-API:
 
* Vid åtkomst till entiteter med hjälp av Media Services REST API, måste du ange specifika namn på huvudfält och värden i HTTP-förfrågningar. Mer information finns i [installationsprogrammet för Media Services REST API-utveckling](media-services-rest-how-to-use.md). <br/>Postman-samling som används i den här självstudien hand tar om att alla nödvändiga rubriker.
* Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömning innehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför stöds procent-kodning inte. Värdet för den **namn** egenskapen får inte innehålla något av följande [procent-encoding-reserverade tecken](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! *' ();: @& = + $, /? [] # % ”. Dessutom det får bara finnas ett '.' för filnamnstillägget.
* Längden på namnet får inte vara större än 260 tecken.
* Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.

## <a name="set-up-postman"></a>Konfigurera Postman

Stegvisa instruktioner för hur du ställer in Postman för den här självstudiekursen finns [konfigurera Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

1. Lägg till anslutningsvärden för din miljö. 

    Några variabler som ingår i den **MediaServices** [miljö](postman-environment.md) måste anges manuellt innan du kan börja köra åtgärder som definierats i den [samling](postman-collection.md).

    Om du vill hämta de värden för de fem första variablerna, se [åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-env.png)
2. Ange värdet för den **MediaFileName** miljövariabeln.

    Ange namnet på media som du planerar att ladda upp. I det här exemplet det dags att ladda upp BigBuckBunny.mp4. 
3. Granska den **AzureMediaServices.postman_environment.json** fil. Du kan se att nästan alla åtgärder i samlingen kör ett ”test” skript. Skripten tar några värden som returneras av svaret lämpliga miljövariablerna.

    Till exempel den första åtgärden hämtar en åtkomsttoken och konfigurera den på den **AccessToken** miljövariabeln som används i alla andra åtgärder.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. Till vänster om den **Postman** fönstret, klicka på **1. Hämta åtkomsttoken för AAD-autentisering** -> **hämta Azure AD-Token för tjänstens huvudnamn**.

    URL-delen är fylld med de **AzureADSTSEndpoint** miljövariabeln (tidigare i självstudien anger du värden för miljövariabler som har stöd för samlingen).

    ![Överför en fil](./media/media-services-rest-upload-files/postment-get-token.png)

5. Tryck på **Skicka**.

    Du kan se ett svar som innehåller ”access_token”. ”Test” skriptet använder det här värdet och anger den **AccessToken** miljövariabeln (enligt beskrivningen ovan). Om du undersöker din miljövariabler visas att den här variabeln innehåller värdet för åtkomst-token (ägartoken) som används i resten av åtgärderna. 

    Om token upphör att gå igenom ”hämta Azure AD-Token för tjänstens huvudnamn” steg igen. 

## <a name="create-an-access-policy-with-write-permission"></a>Skapa en åtkomstprincip med behörighet att skriva

### <a name="overview"></a>Översikt 

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här artikeln](media-services-dotnet-manage-entities.md#limit-access-policies).

Innan du laddar upp filer till blob-lagring, ange principen rättigheter för att skriva till en tillgång. Gör detta genom att publicera en HTTP-begäran till entitetsuppsättning AccessPolicies. Definiera ett DurationInMinutes värde när de skapas eller meddelandet 500 Internt fel i svaret. Läs mer på AccessPolicies [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Skapa en åtkomstprincip

1. Välj **AccessPolicy** -> **skapa AccessPolicy för uppladdning**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skriptet ”test” hämtar AccessPolicy-Id och anger lämpliga miljövariabeln.

## <a name="create-an-asset"></a>Skapa en tillgång

### <a name="overview"></a>Översikt

En [tillgången](https://docs.microsoft.com/rest/api/media/operations/asset) är en behållare för flera typer eller uppsättningar med objekt i Media Services, inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold textning. I REST-API kräver skapa en tillgång skicka POST-begäran till Media Services och placera all egenskapsinformation om din tillgång i begärandetexten.

En av de egenskaper som du kan lägga till när du skapar en tillgång är **alternativ**. Du kan ange något av följande alternativ för kryptering: **Ingen** (standard är Ingen kryptering används), **StorageEncrypted** (för innehåll som har varit förkrypterade med kryptering på klientsidan), **CommonEncryptionProtected**, eller  **EnvelopeEncryptionProtected**. När du har en krypterad tillgång, måste du konfigurera en leveransprincip för. Mer information finns i [konfigurera tillgångsleveransprinciper](media-services-rest-configure-asset-delivery-policy.md).

Om din tillgång krypteras, måste du skapa en **ContentKey** och länkar den till din tillgång enligt beskrivningen i följande artikel: [Så här skapar du en ContentKey](media-services-rest-create-contentkey.md). När du överför filer till tillgången, måste du uppdatera egenskaper för kryptering på den **AssetFile** entitet med de värden som du har fått under den **tillgången** kryptering. Göra det med hjälp av den **sammanfoga** HTTP-begäran. 

I det här exemplet skapar vi en okrypterad tillgång. 

### <a name="create-an-asset"></a>Skapa en tillgång

1. Välj **tillgångar** -> **skapa tillgång**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skriptet ”test” hämtar tillgångs-Id och anger lämpliga miljövariabeln.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Skapa en SAS-lokaliserare och skapa URL för överföring

### <a name="overview"></a>Översikt

När du har AccessPolicy och positionerare ange har faktiska filen överförts till en Azure Blob Storage-behållare med hjälp av Azure Storage REST-API: er. Du måste överföra filerna som blockblobar. Sidblobar stöds inte av Azure Media Services.  

Mer information om hur du arbetar med Azure storage-blobbar finns i [REST-API för Blob Service](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Skapa en SAS-lokaliserare (se nedan) för att ta emot den faktiska URL. Lokaliserare definiera start- och typ av anslutningens slutpunkt för klienter som vill komma åt filer i en tillgång. Du kan skapa flera positionerare entiteter för en viss AccessPolicy och tillgången par att hantera olika klientbegäranden och behov. Var och en av dessa positionerare använder StartTime-värdet plus DurationInMinutes värdet för AccessPolicy för att avgöra hur lång tid som en URL som kan användas. Mer information finns i [positionerare](https://docs.microsoft.com/rest/api/media/operations/locator).

En SAS-URL har följande format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Överväganden

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unik positionerare som är associerade med en given tillgång i taget. Mer information finns i lokaliserare.
* Om du vill ladda upp dina filer omedelbart ska du ange StartTime-värdet till fem minuter före aktuell tid. Det beror på att det kan finnas klockan skeva mellan klientdatorn och Media Services. Dessutom måste StartTime-värdet vara i formatet DateTime: ÅÅÅÅ-MM-: ssZ (till exempel ”2014-05-23T17:53:50Z”).    
* Det kan finnas en 30 – 40 andra fördröjning när en positionerare skapas när den är tillgänglig för användning.

### <a name="create-a-sas-locator"></a>Skapa en SAS-lokaliserare

1. Välj **positionerare** -> **skapa SAS-lokaliserare**.
2. Tryck på **Skicka**.

    Skriptet ”test” skapar ”ladda upp URL” som baseras på det angivna filnamnet media och SAS-positionerare information och anger lämpliga miljövariabeln.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Överföra en fil till blob storage med hjälp av URL

### <a name="overview"></a>Översikt

Nu när du har URL kan behöva du skriva kod med Azure Blob-API: er direkt för att överföra filen till behållaren SAS. Mer information finns i följande artiklar:

- [Använda Azure Storage REST-API](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [PLACERA Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Ladda upp blobar till Blob storage](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Ladda upp en fil med Postman

T.ex använder vi Postman för att överföra en liten MP4-fil. Det kan finnas en storleksgräns för filen för uppladdning binär via Postman.

Överföringsförfrågan är inte en del av den **AzureMedia** samling. 

Skapa och konfigurera en ny begäran:
1. Tryck på **+**, för att skapa en ny flik i begäran.
2. Välj **PLACERA** igen och klistra in **{{UploadURL}}** i URL: en.
2. Lämna **auktorisering** fliken skick (Ange inte till den **ägar-Token**).
3. I den **rubriker** fliken, ange: **Nyckeln**: ”x-ms-blob-type” och **värdet**: "BlockBlob".
2. I den **brödtext** fliken **binära**.
4. Välj fil med namnet som du angav i den **MediaFileName** miljövariabeln.
5. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Skapa en metadata i tillgången

När filen har överförts, måste du skapa en metadata i tillgången för mediefilen som du överfört till blob-lagringen som är associerade med din tillgång.

1. Välj **AssetFiles** -> **CreateFileInfos**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-file-info.png)

Filen ska överföras och ange dess metadata.

## <a name="validate"></a>Verifiera

Kontrollera att filen har överförts har, kanske du vill fråga efter den [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) och jämföra den **ContentFileSize** (eller annan information) till vad du förväntar dig att se i den nya tillgången. 

Till exempel följande **hämta** åtgärden tar fildata tillgången-filen (i eller fallet BigBuckBunny.mp4-fil). Frågan använder den [miljövariabler](postman-environment.md) som du angav tidigare.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Svaret innehåller storlek, namn och annan information.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade containern. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

