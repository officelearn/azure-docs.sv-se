---
title: Azure Media Services v3-SDK:er – Azure
description: Den här artikeln innehåller en översikt över hur du börjar utveckla med Media Services v3 API med hjälp av SDK:er/verktyg.
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
ms.openlocfilehash: d97682de6c8eb7dd36e25cbd4b8d66d328745748
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59543644"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Börja utveckla med Media Services v3 API med hjälp av SDK:er/verktyg

Som utvecklare kan du använda Media Services [REST API](https://aka.ms/ams-v3-rest-ref) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API baseras på OpenAPI-specifikationen (kallades tidigare för en Swagger).

Innan du börjar utveckla granska [utveckla med API: er för Media Services v3](media-services-apis-overview.md).

> [!NOTE]
> SDK: er för Azure Media Services v3 garanteras vara trådsäkra. När du utvecklar ett flertrådiga program, bör du lägga till en egen logik för synkronisering av tråd för att skydda den eller använda ett nytt AzureMediaServicesClient objekt per tråd. Du bör också vara försiktig flertrådsteknik problem som introducerades av valfritt objekt som tillhandahålls av din kod till klienten (t.ex. en HttpClient-instans i .NET).

Det här avsnittet innehåller länkar till SDK: er, verktyg, andra documentaion.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att börja utveckla mot Media Services behöver du:

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Lär dig mer om grundläggande begrepp](concepts-overview.md)
- [Skapa ett Media Services-konto – CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Börja utveckla med SDK:er

### <a name="net"></a>.NET

Använd [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk).

Utforska Media Services [.NET ref](https://aka.ms/ams-v3-dotnet-ref) dokumentation.

### <a name="java"></a>Java

Använd [Java SDK](https://aka.ms/ams-v3-java-sdk).

Granska Media Services [Java-referensdokumentationen](https://aka.ms/ams-v3-java-ref).

### <a name="nodejs"></a>Node.js

Använd [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk).

Utforska Media Services [Node.js-referensdokumentationen](https://aka.ms/ams-v3-nodejs-ref) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.

### <a name="python"></a>Python

Använd [Python SDK](https://aka.ms/ams-v3-python-sdk).

Granska Media Services [Python-referensdokumentationen](https://aka.ms/ams-v3-python-ref).

### <a name="go"></a>Go

Använd [Go SDK](https://aka.ms/ams-v3-go-sdk).

Granska Media Services [Go-referensdokumentationen](https://aka.ms/ams-v3-go-ref).

### <a name="ruby"></a>Ruby

Använd [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta opencode@microsoft.com om du har några andra frågor eller kommentarer.

## <a name="next-steps"></a>Nästa steg

[Översikt](media-services-overview.md)
