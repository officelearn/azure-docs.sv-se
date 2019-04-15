---
title: Azure Media Services v3-SDK:er – Azure
description: 'Den här artikeln innehåller en översikt över hur du börja utveckla med Media Services v3-API med SDK: er.'
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 04/11/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9fb4d1561a661387f759aada9e776d43a95aa5c7
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59564517"
---
# <a name="develop-against-media-services-v3-api-using-sdks"></a>Utveckla mot Media Services v3 API: et med SDK: er

Som utvecklare kan du använda Media Services [REST API](https://aka.ms/ams-v3-rest-ref) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API baseras på OpenAPI-specifikationen (kallades tidigare för en Swagger).

> [!NOTE]
> SDK: er för Azure Media Services v3 garanteras vara trådsäkra. När du utvecklar ett flertrådiga program, bör du lägga till en egen logik för synkronisering av tråd för att skydda den eller använda ett nytt AzureMediaServicesClient objekt per tråd. Du bör också vara försiktig flertrådsteknik problem som introducerades av valfritt objekt som tillhandahålls av din kod till klienten (t.ex. en HttpClient-instans i .NET).

Det här avsnittet innehåller länkar till SDK: er, verktyg, instruktionsguider.

## <a name="prerequisites"></a>Förutsättningar

För att börja utveckla mot Media Services behöver du:

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Lär dig mer om grundläggande begrepp](concepts-overview.md)
- Granska [utveckla med Media Services v3-API: er](media-services-apis-overview.md)
- [Skapa ett Media Services-konto – CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Börja utveckla med SDK:er

### <a name="net"></a>.NET

Använd [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk) till [ansluta till Media Services](configure-connect-dotnet-howto.md).

Utforska Media Services [.NET-referens](https://aka.ms/ams-v3-dotnet-ref) dokumentation.

### <a name="java"></a>Java

Använd [Java SDK](https://aka.ms/ams-v3-java-sdk) till [ansluta till Media Services](configure-connect-java-howto.md).

Granska de tjänster som Media [Java-Referens](https://aka.ms/ams-v3-java-ref) dokumentation.

### <a name="nodejs"></a>Node.js

Använd [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) till [ansluta till Media Services](configure-connect-nodejs-howto.md).

Utforska Media Services [Node.js-referens](https://aka.ms/ams-v3-nodejs-ref) dokumentation och kolla in [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services-API med node.js.

### <a name="python"></a>Python

Använd [Python SDK](https://aka.ms/ams-v3-python-sdk).

Granska de tjänster som Media [Python-referens](https://aka.ms/ams-v3-python-ref) dokumentation.

### <a name="go"></a>Go

Använd [Go SDK](https://aka.ms/ams-v3-go-sdk).

Granska de tjänster som Media [gå referens](https://aka.ms/ams-v3-go-ref) dokumentation.

### <a name="ruby"></a>Ruby

Använd [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta opencode@microsoft.com om du har några andra frågor eller kommentarer.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
