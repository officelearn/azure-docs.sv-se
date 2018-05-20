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
ms.date: 03/27/2018
ms.author: juliako
ms.custom: mvc
ms.openlocfilehash: 33afe5fb24309547a7aacfcbe3b799ed413594ac
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
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
* Implementera en videoplattform för e-utbildning med Azure Media Services och [Azure Cognitive Services-API:er](https://docs.microsoft.com/en-us/azure/#pivot=products&panel=ai) för tal-till-text, översättning till flera språk, o.s.v.
* Aktivera Azure CDN för att uppnå hög skalning för att bättre hantera omedelbar hög belastning (till exempel start av en produktlansering.) 

## <a name="v3-capabilities"></a>v3-funktioner

v3 baseras på en enhetlig API-yta som innehåller funktioner för både hantering och åtgärder som skapats på **Azure Resource Manager**. Den här versionen innehåller följande funktioner:  

* **Transformeringar** som hjälper dig att definiera enkla arbetsflöden för mediebearbetning eller analys. Transformering är ett recept för bearbetning av dina video- och ljudfiler. Du kan sedan använda det flera gånger för att bearbeta alla filer i innehållsbiblioteket, genom att skicka jobb till transformeringen.
* **Jobb** för att bearbeta (koda eller analysera) dina videor. Ett indatainnehåll kan anges för ett jobb med HTTP(s)-URL:er, SAS-URL:er eller sökvägar till filer i Azure Blob Storage. 
* **Meddelanden** som övervakar jobbförlopp eller -tillstånd eller start/stopp av Livekanal och felhändelser. Meddelanden är integrerade med Azure Event Grids meddelandesystem. Du kan enkelt prenumerera på händelser för flera resurser i Azure Media Services. 
* **Azure Resource Manager**-mallar kan användas för att skapa och distribuera transformeringar, strömningsslutpunkter, kanaler med mera.
* **Rollbaserad åtkomstkontroll** kan ställas in på resursnivå, så att du kan låsa åtkomst till specifika resurser som transformeringar och kanaler med mera.
* **Klient-SDK:er** på flera språk: .NET, .NET Core, Python, Go, Java och Node.js.

## <a name="how-can-i-get-started-with-v3"></a>Hur kan jag komma igång med v3?

Som utvecklare kan du använda Media Services [REST API](https://go.microsoft.com/fwlink/p/?linkid=873030) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. Microsoft genererar och har stöd för följande klientbibliotek: 

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [.NET-språk](https://www.nuget.org/packages/Microsoft.Azure.Management.Media/1.0.0)
* .NET Core 
* Java

  Lägg till följande beroende i projektet:
  
  ```
  <dependency>
    <groupId>com.microsoft.azure.media-2018-03-30-preview</groupId>
    <artifactId>azure-mgmt- media</artifactId>
    <version>0.0.1-beta</version>
  </dependency> 
  ```
* Node.js 

  Ange följande kommando:
  
  ```
  npm install azure-arm-mediaservices
  ```
  
* [Python](https://pypi.org/project/azure-mgmt-media/1.0.0rc1/)
* [Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/mediaservices/mgmt/2018-03-30-preview/media)

Media Services tillhandahåller [Swagger-filer](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media) som du kan använda för att generera SDK:er för önskat språk/teknik.  

## <a name="next-steps"></a>Nästa steg

Om du vill se hur lätt det är att börja koda och strömma videofiler, kolla in [Strömma filer](stream-files-dotnet-quickstart.md). 

