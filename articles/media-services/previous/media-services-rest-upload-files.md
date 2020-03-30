---
title: Ladda upp filer till ett Azure Media Services-konto med REST | Microsoft-dokument
description: Läs om hur du får in medieinnehåll i Media Services genom att skapa och ladda upp resurser.
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: d5b84a9d216457720e9bd4e17b002d6ab9490f9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888591"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Ladda upp filer till ett Media Services-konto med REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [Resten](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

I Media Services överför du dina digitala filer till en tillgång. [Asset-entiteten](https://docs.microsoft.com/rest/api/media/operations/asset) kan innehålla video- ljud, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text (och metadata om dessa filer.)  När filerna har överförts till tillgången lagras ditt innehåll säkert i molnet för vidare bearbetning och streaming. 

I den här självstudien får du lära dig hur du laddar upp en fil och annan åtgärd som är associerad med den:

> [!div class="checklist"]
> * Konfigurera Postman för alla uppladdningsåtgärder
> * Ansluta till Media Services 
> * Skapa en åtkomstprincip med skrivbehörighet
> * Skapa en tillgång
> * Skapa en SAS-positionerare och skapa överförings-URL:en
> * Ladda upp en fil till blob-lagring med hjälp av överförings-URL:en
> * Skapa en metadata i tillgången för mediefilen som du laddade upp

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) konto innan du börjar.
- [Skapa ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).
- Granska [API:et för Åtkomst till Azure Media Services med översiktsöversikt för AAD-autentisering.](media-services-use-aad-auth-to-access-ams-api.md)
- Också för mer information Granska [Använd Azure AD-autentisering för att komma åt Api:et för Media Services med REST.](https://docs.microsoft.com/azure/media-services/previous/media-services-rest-connect-with-aad)
- Konfigurera **Postman** enligt beskrivningen i [Konfigurera REST API-anrop för Postman för Media Services](media-rest-apis-with-postman.md)REST .

## <a name="considerations"></a>Överväganden

Följande överväganden gäller när du använder REST-API FÖR Media Services:
 
* När du öppnar entiteter med REST-API för Media Services måste du ange specifika rubrikfält och värden i HTTP-begäranden. Mer information finns i [Installationsprogrammet för REST API Development för Media Services](media-services-rest-how-to-use.md). <br/>Postman-samlingen som används i den här självstudien tar hand om att ange alla nödvändiga rubriker.
* Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL:er för direktuppspelningsinnehållet (till exempel http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Därför är procentkodning inte tillåtet. Värdet för egenskapen **Name** kan inte ha något av följande [procent-kodningsreservat tecken:](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters)!*'();:@&=+$,/?%#[]". Dessutom kan det bara finnas en "." för filnamnstillägget.
* Namnets längd får inte vara större än 260 tecken.
* Det finns en gräns för maximal filstorlek för bearbetning i Media Services. Information om filstorleksbegränsningen finns i [den här](media-services-quotas-and-limitations.md) artikeln.

## <a name="set-up-postman"></a>Konfigurera Postman

Steg om hur du konfigurerar Postman för den här självstudien finns i [Konfigurera Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Ansluta till Media Services

1. Lägg till anslutningsvärden i din miljö. 

    Vissa variabler som ingår i **MediaServices-miljön** [environment](postman-environment.md) måste ställas in manuellt innan du kan börja köra åtgärder som definierats i [samlingen](postman-collection.md).

    Information om hur du hämtar värden för de fem första variablerna finns [i Komma åt Azure Media Services API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Överför en fil](./media/media-services-rest-upload-files/postman-import-env.png)
2. Ange värdet för **mediefilename-miljövariabeln.**

    Ange filnamnet på det medium som du planerar att ladda upp. I det här exemplet kommer vi att ladda upp BigBuckBunny.mp4. 
3. Undersök **filen AzureMediaServices.postman_environment.json.** Du kommer att se att nästan alla åtgärder i samlingen kör ett "test" skript. Skripten tar vissa värden som returneras av svaret och ange lämpliga miljövariabler.

    Den första åtgärden får till exempel en åtkomsttoken och ställer in den på **accesstoken-miljövariabeln** som används i alla andra åtgärder.

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
4. Klicka på 1 till vänster om **Postman-fönstret.** ** Hämta AAD Auth-token** -> **Hämta Azure AD-token för tjänsthuvudnamn**.

    URL-delen är fylld med miljön variabeln **AzureADSTSEndpoint** (tidigare i självstudien anger du värdena för miljövariabler som stöder samlingen).

    ![Överför en fil](./media/media-services-rest-upload-files/postment-get-token.png)

5. Tryck på **Skicka**.

    Du kan se svaret som innehåller "access_token". Skriptet "test" tar det här värdet och anger **accesstoken-miljövariabeln** (enligt beskrivningen ovan). Om du undersöker dina miljövariabler ser du att den här variabeln nu innehåller värdet för åtkomsttoken (innehavartoken) som används i resten av åtgärderna. 

    Om token upphör att gälla går du igenom steget "Hämta Azure AD-token för tjänsthuvudnamn" igen. 

## <a name="create-an-access-policy-with-write-permission"></a>Skapa en åtkomstprincip med skrivbehörighet

### <a name="overview"></a>Översikt 

>[!NOTE]
>Det finns en gräns på 1 000 000 principer för olika AMS-principer (till exempel för positionerarprincipen eller ContentKeyAuthorizationPolicy). Du bör använda samma princip-ID om du alltid använder samma dagar/åtkomstbehörigheter, till exempel principer för positionerare som är avsedda att vara på plats under en längre tid (icke-överföringsprinciper). Mer information finns i [den här](media-services-dotnet-manage-entities.md#limit-access-policies) artikeln.

Innan du överför filer till blob-lagring anger du åtkomstprinciprättigheterna för att skriva till en tillgång. Det gör du genom att publicera en HTTP-begäran till entityten AccessPolicies. Definiera ett DurationInMinutes-värde när du skapar eller så visas ett felmeddelande om 500 intern server som svar. Mer information om AccessPolicies finns i [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Skapa en åtkomstprincip

1. Välj **AccessPolicy** -> **Skapa AccessPolicy för överföring**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-access-policy.png)

    Skriptet "test" får AccessPolicy-ID:t och anger rätt miljövariabel.

## <a name="create-an-asset"></a>Skapa en tillgång

### <a name="overview"></a>Översikt

En [tillgång](https://docs.microsoft.com/rest/api/media/operations/asset) är en behållare för flera typer eller uppsättningar av objekt i Media Services, inklusive video, ljud, bilder, miniatyrsamlingar, textspår och filer med dold text. I REST API, skapa en tillgång kräver att skicka POST begäran till Media Services och placera någon egendom information om din tillgång i begäran kroppen.

En av de egenskaper som du kan lägga till när du skapar en tillgång är **Alternativ**. Du kan ange något av följande krypteringsalternativ: **Ingen** (standard, ingen kryptering används), **StorageEncrypted** (för innehåll som har förkrypterats med lagringskryptering på klientsidan), **CommonEncryptionProtected**eller **EnvelopeEncryptionProtected**. När du har en krypterad tillgång måste du konfigurera en leveransprincip. Mer information finns i [Konfigurera principer för tillgångsleverans](media-services-rest-configure-asset-delivery-policy.md).

Om din tillgång är krypterad måste du skapa en **ContentKey** och länka den till din tillgång enligt beskrivningen i följande artikel: [Så här skapar du en ContentKey](media-services-rest-create-contentkey.md). När du har laddat upp filerna till tillgången måste du uppdatera krypteringsegenskaperna på **AssetFile-entiteten** med de värden du fick under **tillgångskrypteringen.** Gör det med hjälp av **KOPPLA** HTTP-begäran. 

I det här exemplet skapar vi en okrypterad tillgång. 

### <a name="create-an-asset"></a>Skapa en tillgång

1. Välj **Tillgångar** -> **Skapa tillgång**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-asset.png)

    Skriptet "test" hämtar tillgångs-ID:t och anger rätt miljövariabel.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Skapa en SAS-positionerare och skapa uppladdnings-URL:en

### <a name="overview"></a>Översikt

När du har angett AccessPolicy och Locator överförs den faktiska filen till en Azure Blob Storage-behållare med Azure Storage REST API:er. Du måste ladda upp filerna som blockblobar. Sidblobar stöds inte av Azure Media Services.  

Mer information om hur du arbetar med Azure storage blobbar finns i [Blob Service REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Om du vill ta emot den faktiska överförings-URL:en skapar du en SAS-positionerare (visas nedan). Positionerare definierar starttid och typ av anslutningsslutpunkt för klienter som vill komma åt filer i en tillgång. Du kan skapa flera positionerarentiteter för ett givet AccessPolicy- och Tillgångspar för att hantera olika klientbegäranden och behov. Var och en av dessa positionerare använder StartTime-värdet plus värdet DurationInMinutes för AccessPolicy för att bestämma hur länge en URL kan användas. Mer information finns i [Locator](https://docs.microsoft.com/rest/api/media/operations/locator).

En SAS-URL har följande format:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Överväganden

Vissa förutsättningar gäller:

* Du kan inte ha fler än fem unika positionerare som är associerade med en viss tillgång samtidigt. Mer information finns i Locator.
* Om du behöver ladda upp dina filer omedelbart bör du ställa in StartTime-värdet till fem minuter före den aktuella tiden. Detta beror på att det kan finnas en klocksnedställning mellan klientdatorn och Media Services. Starttime-värdet måste också vara i följande DateTime-format: YYYY-MM-DDTHH:mm:ssZ (till exempel "2014-05-23T17:53:50Z").    
* Det kan finnas en fördröjning på 30-40 sekunder efter att en sökare har skapats till när den är tillgänglig för användning.

### <a name="create-a-sas-locator"></a>Skapa en SAS-positionerare

1. Välj **Positionerare** -> **Skapa SAS-positionerare**.
2. Tryck på **Skicka**.

    Skriptet "test" skapar "Upload URL" baserat på det mediefilnamn du angav och SAS-positionerarinformation och anger lämplig miljövariabel.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Ladda upp en fil till blob-lagring med hjälp av överförings-URL:en

### <a name="overview"></a>Översikt

Nu när du har överförings-URL:en måste du skriva en kod med Azure Blob API:er direkt för att ladda upp filen till SAS-behållaren. Mer information finns i följande artiklar:

- [Använda Azure Storage REST API](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [SÄTT Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Ladda upp blobbar till Blob-lagring](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Ladda upp en fil med Postman

Som ett exempel använder vi Postman för att ladda upp en liten MP4-fil. Det kan finnas en filstorleksgräns för att överföra binära via Postman.

Överföringsbegäran är inte en del av **AzureMedia-samlingen.** 

Skapa och konfigurera en ny begäran:
1. Tryck **+** på om du vill skapa en ny begäranflik.
2. Välj **PUT-åtgärd** och klistra in **{{UploadURL}}** i URL:en.
2. Låt **auktoriseringsfliken** som den är (ställ inte in den på **innehavartoken**).
3. På fliken **Rubriker** anger du: **Nyckel:**"x-ms-blob-type" och **Värde**: "BlockBlob".
2. Klicka på **binärt**på fliken **Brödtext.**
4. Välj filen med det namn som du angav i miljövariabeln **MediaFileName.**
5. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Skapa en metadata i tillgången

När filen har överförts måste du skapa en metadata i tillgången för mediefilen som du laddade upp till blob-lagringen som är associerad med din tillgång.

1. Välj **AssetFiles** -> **CreateFileInfos**.
2. Tryck på **Skicka**.

    ![Överför en fil](./media/media-services-rest-upload-files/postman-create-file-info.png)

Filen ska överföras och dess metadata anges.

## <a name="validate"></a>Verifiera

Om du vill verifiera att filen har överförts kan du fråga [assetfile](https://docs.microsoft.com/rest/api/media/operations/assetfile) och jämföra **ContentFileSize** (eller annan information) med vad du förväntar dig att se i den nya tillgången. 

Följande **GET-åtgärd** ger till exempel fildata för din tillgångsfil (i eller fallet filen BigBuckBunny.mp4). Frågan använder de [miljövariabler](postman-environment.md) som du angav tidigare.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

Svaret innehåller storlek, namn och annan information.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Nästa steg

Du kan nu koda överförda tillgångar. Mer information finns i [Koda tillgångar](media-services-portal-encode.md).

Du kan också använda Azure Functions för att utlösa ett kodningsjobb baserat på en fil som skickas till den konfigurerade containern. Mer information finns i [det här exemplet](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

