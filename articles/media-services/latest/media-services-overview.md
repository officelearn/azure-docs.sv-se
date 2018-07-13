---
title: Översikt över Azure Media Services v3 | Microsoft Docs
description: Den här artikeln innehåller en översikt av Media Services och innehåller länkar till artiklar för mer information.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: azure media services, strömma, sändning, live, offline
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 06/14/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 5205a6746f6a698768a60375e2e77db9cb535a71
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38971915"
---
# <a name="what-is-azure-media-services-v3"></a>Vad är Azure Media Services v3?

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [Version 2 – allmänt tillgänglig](../previous/media-services-overview.md)
> * [Version 3 – förhandsversion](media-services-overview.md)

> [!NOTE]
> Den senaste versionen av Azure Media Services är en förhandsversion och kan kallas v3.

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
* **Jobb** för att bearbeta (koda eller analysera) dina videor. Ett indatainnehåll kan anges för ett jobb med HTTP(s)-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage. 
* **Meddelanden** som övervakar jobbförlopp eller -tillstånd eller start/stopp av Livekanal och felhändelser. Meddelanden är integrerade med Azure Event Grids meddelandesystem. Du kan enkelt prenumerera på händelser för flera resurser i Azure Media Services. 
* **Azure Resource Manager**-mallar kan användas för att skapa och distribuera transformeringar, strömningsslutpunkter, kanaler med mera.
* **Rollbaserad åtkomstkontroll** kan ställas in på resursnivå, så att du kan låsa åtkomst till specifika resurser som transformeringar och kanaler med mera.
* **Klient-SDK:er** på flera språk: .NET, .NET Core, Python, Go, Java och Node.js.

## <a name="naming-conventions"></a>Namngivningskonventioner

Azure Media Services v3-resursnamn (till exempel tillgångar, jobb, transformeringar) är föremål Azure Resource Manager-namnbegränsningar. I enlighet med Azure Resource Manager är resursnamnen alltid unika. Därför kan du använda alla strängar som unika identifierare (till exempel GUID) för ditt resursnamn. 

Media Services resursnamn får inte innehålla: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', apostrof eller några andra kontrolltecken. Alla andra tecken tillåts. Maxlängden för ett resursnamn är 260 tecken. 

Mer information om namngivning av Azure Resource Manager finns i: [Namngivningskrav](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) och [Namngivningskonventioner](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3?

Som utvecklare kan du använda Media Services [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. Du kan hitta exempel REST Postman [här](https://github.com/Azure-Samples/media-services-v3-rest-postman). Du kan också använda [Azure Resource Manager baserad REST API](https://github.com/Azure-Samples/media-services-v3-arm-templates).

Microsoft genererar och har stöd för följande klientbibliotek: 

|Klientbibliotek|Exempel|
|---|---|
|[Azure CLI SDK](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)|[Azure CLI-exempel](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)|
|[.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)|[.NET-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials)|
|[.NET Core SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0) (välj fliken **.NET CLI**)|[.NET Core-exempel](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials)|
|[Java SDK](https://docs.microsoft.com/java/api/overview/azure/mediaservices)||
|[Node.js SDK](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/index?view=azure-node-latest)|[Node.js-exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials)|
|[Python SDK](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)||
|[Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)||

Media Services tillhandahåller [Swagger-filer](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) som du kan använda för att generera SDK:er för önskat språk/teknik.  

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

