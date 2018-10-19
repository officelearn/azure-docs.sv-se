---
title: Översikt över Azure Media Services v3 | Microsoft Docs
description: Den här artikeln innehåller en översikt av Media Services och innehåller länkar till artiklar för mer information.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 10/16/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 66c9b8297254b41156131d795376857029424e97
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377925"
---
# <a name="what-is-azure-media-services-v3"></a>Vad är Azure Media Services v3?

Azure Media Services är en molnbaserad plattform för att skapa lösningar för videor i sändningskvalitet för direktuppspelning, förbättra tillgängligheten och distributionen, analysera innehållet och mycket mer. Om du är en programutvecklare, ett callcenter, en myndighet eller ett underhållningsföretag kan Media Services hjälpa dig att skapa program som levererar medieupplevelser med fantastisk kvalitet för stora målgrupper på dagens populäraste mobila enheter och webbläsare. 

## <a name="what-can-i-do-with-media-services"></a>Vad kan jag göra med Media Services?

Media Services hjälper dig att skapa en mängd olika mediearbetsflöden i molnet. Följande är några exempel på vad som kan utföras med Media Services.  

* Leverera video i olika format, så att de kan spelas upp på en mängd olika webbläsare och enheter. För både strömning på begäran och liveuppspelning till olika klienter (mobila enheter, TV, datorer, etc.), måste video- och ljudinnehållet vara kodat och paketerat korrekt. Information om hur du levererar och strömmar innehållet finns i [Snabbstart: koda och strömma filer](stream-files-dotnet-quickstart.md).
* Strömma sporthändelser live till en stor online-publik, till exempel fotboll, baseball, universitets- och gymnasiesporter med mera. 
* Sänd offentliga möten och händelser såsom för kommunalråd, statsråd och lagstiftande organ.
* Analysera inspelat video- eller ljudinnehåll. För att till exempel uppnå högre kundnöjdhet kan organisationer extrahera tal till text och skapa sökindex och instrumentpaneler. De kan sedan extrahera information kring vanliga klagomål, källor till klagomål och andra relevanta data. 
* Skapa en prenumerationsvideotjänst och strömma DRM-skyddat innehåll när en kund (till exempel en filmstudio) behöver begränsa åtkomst och användning av upphovsrättsskyddat arbete.
* Leverera offline-innehåll för uppspelning på flygplan tåg och bilar. En kund kan behöva ladda ner innehåll till sin telefon eller surfplatta för uppspelning när de vet att de kan kopplas ned från nätverket.
* Lägg till textning och beskrivningar till videor för att tillgodose en bredare publik (till exempel personer med handikapp) eller användare som vill kunna läsa med i ett annat språk. 
* Implementera en videoplattform för e-utbildning med Azure Media Services och [Azure Cognitive Services-API:er](https://docs.microsoft.com/azure/#pivot=products&panel=ai) för tal-till-text, översättning till flera språk, o.s.v.
* Aktivera Azure CDN för att uppnå hög skalning för att bättre hantera omedelbar hög belastning (till exempel start av en produktlansering.) 

## <a name="v3-capabilities"></a>v3-funktioner

v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på Azure Resource Manager. 

Den här versionen innehåller följande funktioner:  

* **Transformeringar** som hjälper dig att definiera enkla arbetsflöden för mediebearbetning eller analys. Transformering är ett recept för bearbetning av dina video- och ljudfiler. Du kan sedan använda det flera gånger för att bearbeta alla filer i innehållsbiblioteket, genom att skicka jobb till transformeringen.
* **Jobb** för att bearbeta (koda eller analysera) dina videor. Ett indatainnehåll kan anges för ett jobb med HTTP-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage. AMS v3 stöder för närvarande inte segmentvis överföringskodning över HTTPS-URL:er.
* **Meddelanden** som övervakar jobbförlopp eller -tillstånd eller start/stopp av Livekanal och felhändelser. Meddelanden är integrerade med Azure Event Grids meddelandesystem. Du kan enkelt prenumerera på händelser för flera resurser i Azure Media Services. 
* **Azure Resource Manager**-mallar kan användas för att skapa och distribuera transformeringar, strömningsslutpunkter, kanaler med mera.
* **Rollbaserad åtkomstkontroll** kan ställas in på resursnivå, så att du kan låsa åtkomst till specifika resurser som transformeringar och kanaler med mera.
* **Klient-SDK:er** på flera språk: .NET, .NET Core, Python, Go, Java och Node.js.

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="media-services-v3-api-design-principles"></a>Designprinciper för Media Services, v3 API

En av de viktigaste designprinciperna för v3 API är att göra API:et säkrare. v3 API:er returnerar inte hemlighet eller autentiseringsuppgifter för en åtgärd av typen **hämta** eller **lista**. Nycklarna är alltid null, tomma eller oberoende av svaret. Du måste anropa en separat åtgärdsmetod för att få hemlighet och autentiseringsuppgifter. Med separata åtgärder kan du ställa in olika RBAC-säkerhetsbehörigheter om vissa API:er hämtar/visar hemligheter medan andra API:er inte gör det. Information om hur du hanterar åtkomst med RBAC finns i dokumentationen om att [hantera åtkomst med RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Exempel på det här kan vara att 

* inte returnera ContentKey-värden i Hämta för StreamingLocator, 
* inte returnera begränsningsnycklar för get för ContentKeyPolicy, 
* inte returnera frågesträngsdelen av webbadressen (för att ta bort signaturen) för jobbets HTTP-indatawebbadresser.

I följande .NET-exempel visar vi hur du hämtar en signeringsnyckel från den befintliga principen. Du måste använda **GetPolicyPropertiesWithSecretsAsync** för att få nyckeln.

```csharp
private static async Task<ContentKeyPolicy> GetOrCreateContentKeyPolicyAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string contentKeyPolicyName)
{
    ContentKeyPolicy policy = await client.ContentKeyPolicies.GetAsync(resourceGroupName, accountName, contentKeyPolicyName);

    if (policy == null)
    {
        // Configure and create a new policy.
        
        . . . 
        policy = await client.ContentKeyPolicies.CreateOrUpdateAsync(resourceGroupName, accountName, contentKeyPolicyName, options);
    }
    else
    {
        var policyProperties = await client.ContentKeyPolicies.GetPolicyPropertiesWithSecretsAsync(resourceGroupName, accountName, contentKeyPolicyName);
        var restriction = policyProperties.Options[0].Restriction as ContentKeyPolicyTokenRestriction;
        if (restriction != null)
        {
            var signingKey = restriction.PrimaryVerificationKey as ContentKeyPolicySymmetricTokenKey;
            if (signingKey != null)
            {
                TokenSigningKey = signingKey.KeyValue;
            }
        }
    }

    return policy;
}
```

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3?

Som utvecklare kan du använda Media Services [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden.  

Media Services tillhandahåller [Swagger-filer](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) som du kan använda för att generera SDK:er för önskat språk/teknik.  

Microsoft genererar och har stöd för följande klientbibliotek: 

|API-referenser|SDK:er/verktyg|Exempel|
|---|---|---|---|
|[REST-referens](https://aka.ms/ams-v3-rest-ref)|[REST-SDK](https://aka.ms/ams-v3-rest-sdk)|[REST-exempel för Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman)<br/>[Azure Resource Manager-baserat REST-API](https://github.com/Azure-Samples/media-services-v3-arm-templates)|
|[Azure CLI-referens](https://aka.ms/ams-v3-cli-ref)|[Azure CLI](https://aka.ms/ams-v3-cli)|[Azure CLI-exempel](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)||
|[.NET-referens](https://aka.ms/ams-v3-dotnet-ref)|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)||
||[.NET Core SDK](https://aka.ms/ams-v3-dotnet-sdk) (välj fliken **.NET CLI**)|[.NET Core-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)||
|[Java-referens](https://aka.ms/ams-v3-java-ref)|[Java SDK](https://aka.ms/ams-v3-java-sdk)||
|[Node.js-referens](https://aka.ms/ams-v3-nodejs-ref)|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)|[Node.js-exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials)||
|[Python-referens](https://aka.ms/ams-v3-python-ref)|[Python SDK](https://aka.ms/ams-v3-python-sdk)||
|[Go-referens](https://aka.ms/ams-v3-go-ref)|[Go SDK](https://aka.ms/ams-v3-go-sdk)||
|Ruby|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

