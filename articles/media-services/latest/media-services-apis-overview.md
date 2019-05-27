---
title: 'Utveckla med v3 API: er – Azure | Microsoft Docs'
description: 'Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.'
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: ca4e343ea4774bbe4ff992ad671575b150b3c045
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890976"
---
# <a name="developing-with-media-services-v3-apis"></a>Utveckla med Media Services v3-API: er

Som utvecklare kan du använda Media Services [REST API](https://aka.ms/ams-v3-rest-ref) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API baseras på OpenAPI-specifikationen (kallades tidigare för en Swagger).

Den här artikeln beskrivs regler som gäller för entiteter och API: er när du utvecklar med Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Åtkomst till Azure Media Services API

Om du vill ha behörighet att komma åt Media Services-resurser och Media Services-API, måste du först autentiseras. Media Services stöder [Azure Active Directory (Azure AD)-baserade](../../active-directory/fundamentals/active-directory-whatis.md) autentisering. Det finns två vanliga alternativ för autentisering:
 
* **Autentisering av tjänstens huvudnamn** – används för att autentisera en tjänst (till exempel: web apps, funktionsappar, logic apps, API och mikrotjänster). Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, mellannivå tjänster eller schemalagda jobb. För webben vara program det till exempel alltid ett medelnivån som ansluter till Media Services med ett huvudnamn för tjänsten.
* **Användarautentisering** – används för att autentisera en person som använder appen för att interagera med Media Services-resurser. Interaktivt program bör först frågar användaren om användarens autentiseringsuppgifter. Ett exempel är en management-konsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning.

Media Services-API kräver att användaren eller programmet att göra REST API-begäranden har åtkomst till resursen i Media Services-konto och använder en **deltagare** eller **ägare** roll. API: et kan användas med den **läsare** roll men endast **hämta** eller **lista**   operations blir tillgängliga. Mer information finns i [rollbaserad åtkomstkontroll för Media Services-konton](rbac-overview.md).

Överväg att använda hanterade identiteter för Azure-resurser för att få åtkomst till Media Services-API via Azure Resource Manager istället för att skapa ett huvudnamn för tjänsten. Läs mer om hanterade identiteter för Azure-resurser i [vad är hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Azure AD-tjänstens huvudnamn 

Om du skapar en Azure AD-program och tjänstens huvudnamn kan måste programmet vara i sin egen klient. När du har skapat programmet kan ge appen **deltagare** eller **ägare** platssystemrollens åtkomst till Media Services-kontot. 

Om du inte är säker på om du har behörighet att skapa ett Azure AD-program, se [behörigheter som krävs för](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

I följande bild representerar talen flödet av begäranden i kronologisk ordning:

![Mellannivå appar](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. En mellannivå appen begär en åtkomsttoken för Azure AD som har följande parametrar:  

   * Azure AD tenant-slutpunkten.
   * Media Services resurs-URI.
   * Resurs-URI för REST Media Services.
   * Azure AD application värden: klient-ID och klienthemlighet.
   
   Om du vill hämta värdena som behövs, se [åtkomst till Azure Media Services-API med Azure CLI](access-api-cli-how-to.md)

2. Azure AD-åtkomsttoken skickas till mellannivån.
4. På mellannivå skickar begäran till Azure Media REST-API med Azure AD-token.
5. Mellannivån får tillbaka data från Media Services.

### <a name="samples"></a>Exempel

Se följande exempel som visar hur du ansluter med Azure AD-tjänstens huvudnamn:

* [Ansluta med REST](media-rest-apis-with-postman.md)  
* [Ansluta med Java](configure-connect-java-howto.md)
* [Ansluta med .NET](configure-connect-dotnet-howto.md)
* [Ansluta med .Node.js](configure-connect-nodejs-howto.md)
* [Ansluta med Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Långvariga åtgärder

Åtgärderna som markerats med `x-ms-long-running-operation` i Azure Media Services [swagger filer](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) är långa åtgärder. 

Mer information om hur du spårar asynkrona åtgärder i Azure finns [asynkrona åtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services har följande långvariga åtgärder:

* Skapa LiveEvent
* Uppdatera LiveEvent
* Delete LiveEvent
* Starta LiveEvent
* Stoppa LiveEvent
* Återställ LiveEvent
* Skapa LiveOutput
* Delete LiveOutput
* Skapa StreamingEndpoint
* Uppdatera StreamingEndpoint
* Ta bort StreamingEndpoint
* Starta StreamingEndpoint
* Stoppa StreamingEndpoint
* Skala StreamingEndpoint


## <a name="sdks"></a>SDK:er

> [!NOTE]
> SDK: er för Azure Media Services v3 garanteras vara trådsäkra. När du utvecklar ett flertrådiga program, bör du lägga till en egen logik för synkronisering av tråd för att skydda den eller använda ett nytt AzureMediaServicesClient objekt per tråd. Du bör också vara försiktig flertrådsteknik problem som introducerades av valfritt objekt som tillhandahålls av din kod till klienten (t.ex. en HttpClient-instans i .NET).

|SDK|Referens|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referens](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referens](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referens](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referens](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referens](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Se också

- [EventGrid .NET SDK, som innehåller Media Service-händelser](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definitioner av Media Services-händelser](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta opencode@microsoft.com om du har några andra frågor eller kommentarer.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrering, skrivordning, växling av Media Services-entiteter

Se [filtrering, sortering, växling av Azure Media Services-entiteter](entities-overview.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Nästa steg

* [Ansluta till Media Services med Java](configure-connect-java-howto.md)
* [Ansluta till Media Services med .NET](configure-connect-dotnet-howto.md)
* [Ansluta till Media Services med Node.js](configure-connect-nodejs-howto.md)
* [Ansluta till Media Services med Python](configure-connect-python-howto.md)
