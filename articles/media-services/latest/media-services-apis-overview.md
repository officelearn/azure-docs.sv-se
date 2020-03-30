---
title: Utveckla med v3 API:er
titleSuffix: Azure Media Services
description: Läs mer om regler som gäller för entiteter och API:er när du utvecklar med Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472092"
---
# <a name="develop-with-media-services-v3-apis"></a>Utveckla med Media Services v3 API:er

Som utvecklare kan du använda Media Services [REST API](https://docs.microsoft.com/rest/api/media/) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Api:et för Media Services v3](https://aka.ms/ams-v3-rest-sdk) baseras på OpenAPI-specifikationen (tidigare känd som Swagger).

I den här artikeln beskrivs regler som gäller för entiteter och API:er när du utvecklar med Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Komma åt API:et för Azure Media Services

Om du vill ha åtkomst till Media Services-resurser och API:et för Medietjänster måste du först autentiseras. Media Services stöder [Azure Active Directory (Azure AD)-baserad](../../active-directory/fundamentals/active-directory-whatis.md) autentisering. Två vanliga autentiseringsalternativ är:
 
* **Tjänstens huvudautentisering**: Används för att autentisera en tjänst (till exempel webbappar, funktionsappar, logikappar, API och mikrotjänster). Program som ofta använder den här autentiseringsmetoden är appar som kör daemontjänster, mellannivåtjänster eller schemalagda jobb. För webbappar bör det till exempel alltid finnas en mellannivå som ansluter till Media Services med en tjänsthuvudnamn.
* **Användarautentisering**: Används för att autentisera en person som använder appen för att interagera med Media Services-resurser. Den interaktiva appen bör först fråga användaren om användarens autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller livestreaming.

Api:et för Media Services kräver att användaren eller appen som gör REST API-begäranden har åtkomst till Media Services-kontoresursen och använder en **deltagar-** eller **ägarroll.** API:et kan nås med **rollen Läsare,** men endast **åtgärder för hämta** eller **lista** kommer att vara tillgängliga.Mer information finns i [Rollbaserad åtkomstkontroll för Media Services-konton](rbac-overview.md).

I stället för att skapa ett tjänsthuvudnamn bör du överväga att använda hanterade identiteter för Azure-resurser för att komma åt Api:et för medietjänster via Azure Resource Manager. Mer information om hanterade identiteter för Azure-resurser finns i [Vad är hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Huvudnamn för Azure AD-tjänsten

Om du skapar en Azure AD-app och tjänsthuvudnamn måste appen finnas i sin egen klientorganisation. När du har skapat appen ger du appen **Contributor** eller **Owner** roll åtkomst till Media Services-kontot.

Om du är osäker på om du har behörighet att skapa en Azure AD-app läser [du Behörigheter för obligatoriskt.](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)

I följande figur representerar siffrorna flödet av begäranden i kronologisk ordning:

![Appautentisering på mellannivå med AAD från ett webb-API](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. En app på mellannivå begär en Azure AD-åtkomsttoken som har följande parametrar:  

   * Slutpunkten för Azure AD-klient.
   * Resurs-URI för Media Services.
   * Resurs-URI för REST Media Services.
   * Azure AD-appvärden: klient-ID och klienthemlighet.

   Information om hur du hämtar alla nödvändiga värden finns i [Api för Åtkomst till Azure Media Services med Azure CLI](access-api-cli-how-to.md).

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. Den mellandelade nivån skickar begäran till Azure Media REST API med Azure AD-token.
5. Den mellersta nivån hämtar data från Media Services.

### <a name="samples"></a>Exempel

Se följande exempel som visar hur du ansluter till Azure AD-tjänstens huvudnamn:

* [Anslut med REST](media-rest-apis-with-postman.md)  
* [Ansluta med Java](configure-connect-java-howto.md)
* [Ansluta med .NET](configure-connect-dotnet-howto.md)
* [Ansluta med .Node.js](configure-connect-nodejs-howto.md)
* [Ansluta med Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn.

Resursnamn för Medietjänster kan inte innehålla: "<", ">", %', "&", ":", "&#92;", "?", "/", "*", "+", ".". Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken.

Mer information om namngivning av Azure Resource Manager finns i [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [namngivningskonventioner](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Namn på filer/blobbar i en tillgång

Namnen på filer/blobbar i en tillgång måste följa både [blobnamnskraven](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) och [NTFS-namnkraven](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Orsaken till dessa krav är att filerna kan kopieras från blob-lagring till en lokal NTFS-disk för bearbetning.

## <a name="long-running-operations"></a>Långvariga operationer

De åtgärder `x-ms-long-running-operation` som markerats med i Azure Media Services [swagger-filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är tidskrävande åtgärder. 

Mer information om hur du spårar asynkrona Azure-åtgärder finns i [Async-åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services har följande långvariga åtgärder:

* [Skapa livehändelser](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Uppdatera livehändelser](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Ta bort livehändelse](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Starta livehändelse](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Stoppa LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Använd `removeOutputsOnStop` parametern för att ta bort alla associerade liveutdata när du stoppar händelsen.  
* [Återställ LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Skapa LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Ta bort LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Skapa streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Uppdatera streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Ta bort StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Starta StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Stoppa streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Skala streamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

När en lång åtgärd har skickats får du ett "202 Accepterat" och måste avsöka för att åtgärden ska slutföras med det returnerade åtgärds-ID:et.

I artikeln [spåra asynkrona Azure-åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) beskrivs ingående hur du spårar status för asynkrona Azure-åtgärder genom värden som returneras i svaret.

Endast en tidskrävande åtgärd stöds för en viss Live-händelse eller någon av dess associerade liveutdata. När du har startat måste en tidskrävande åtgärd slutföras innan en efterföljande tidskrävande åtgärd startas på samma LiveEvent eller tillhörande liveutdata. För livehändelser med flera liveutdata måste du invänta slutförandet av en tidskrävande åtgärd på en Live Output innan du utlöser en tidskrävande åtgärd på en annan Live Output. 

## <a name="sdks"></a>SDK:er

> [!NOTE]
> Azure Media Services v3 SDK:er är inte garanterade att vara trådsäkra. När du utvecklar en app med flera trådar bör du lägga till din egen trådsynkroniseringslogik för att skydda klienten eller använda ett nytt AzureMediaServicesClient-objekt per tråd. Du bör också vara försiktig med problem med flera trådar som introduceras av valfria objekt som tillhandahålls av koden till klienten (till exempel en HttpClient-instans i .NET).

|SDK|Referens|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referens](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referens](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referens](https://aka.ms/ams-v3-python-ref)|
|[Nod.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referens](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referens](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Se även

- [EventGrid .NET SDK som innehåller mediatjänsthändelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av mediatjänster händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). För mer information, se [uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) opencode@microsoft.com FAQ eller kontakt med andra frågor eller kommentarer.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, beställning, växling av Medietjänster

Se [Filtrering, beställning, växling av Azure Media Services-entiteter](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in [communityartikeln i Azure Media Services](media-services-community.md) för att se olika sätt att ställa frågor, ge feedback och få uppdateringar om Medietjänster.

## <a name="see-also"></a>Se även

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till medietjänster med Java](configure-connect-java-howto.md)
* [Ansluta till Media Services med .NET](configure-connect-dotnet-howto.md)
* [Ansluta till Medietjänster med nod.js](configure-connect-nodejs-howto.md)
* [Ansluta till Medietjänster med Python](configure-connect-python-howto.md)
