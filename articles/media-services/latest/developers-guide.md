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
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: cda6c9cd1f9c8b9305349f0904aeb744ba373711
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350257"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Börja utveckla med Media Services v3 API med hjälp av SDK:er/verktyg

Som utvecklare kan du använda Media Services [REST API](https://aka.ms/ams-v3-rest-ref) eller klientbibliotek så att du kan interagera med REST API för att enkelt skapa, hantera och underhålla anpassade mediearbetsflöden. [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API baseras på OpenAPI-specifikationen (kallades tidigare för en Swagger).

Det här avsnittet innehåller länkar till SDK:er, verktyg och dokumentation. Där finns även användbar information för olika utvecklarmiljöer.

## <a name="prerequisites"></a>Förutsättningar

För att börja utveckla mot Media Services behöver du:

- En aktiv Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
- [Lär dig mer om grundläggande begrepp](concepts-overview.md)
- [Skapa ett Media Services-konto](create-account-cli-how-to.md)

## <a name="start-developing"></a>Börja utveckla

Azure Media Services har stöd för följande SDK:er/verktyg 

- [Azure CLI](https://aka.ms/ams-v3-cli) 
- [.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk)
- [Python SDK](https://aka.ms/ams-v3-python-sdk)
- [Go SDK](https://aka.ms/ams-v3-go-sdk)
- [Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) är ett verktyg som är tillgängligt för Windows-kunder som vill lära sig om Media Services. AMSE är ett Winforms-/C#-program som laddar upp, laddar ned, kodar och strömmar VOD- och live-innehåll med Media Services. AMSE-verktyget är till för klienter som vill testa Media Services utan att skriva någon kod. AMSE-koden tillhandahålls som en resurs för kunder som vill utveckla med Media Services.

AMSE är ett projekt med öppen källkod och får support från communityn (problem kan rapporteras till https://github.com/Azure/Azure-Media-Services-Explorer/issues). Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta opencode@microsoft.com om du har några andra frågor eller kommentarer.

## <a name="rest"></a>REST

För att börja utveckla med Media Services REST-API:er installerar du en REST-klient. Exempel kan vara **Postman**, **Visual Studio Code** med REST-plugin-programmet eller **Telerik Fiddler**. Vi använder [Postman](media-rest-apis-with-postman.md) för Media Services v3-exempel.

Utforska Media Services [REST API-referensdokumentationen](https://aka.ms/ams-v3-rest-ref) och titta på de här exemplen:

- [Självstudier: Koda en fjärrfil baserat på URL och strömma videon – REST](stream-files-tutorial-with-rest.md)
- [Ladda upp filer till ett Media Services-konto – REST](upload-files-rest-how-to.md)
- [Skapa filter med Media Services – REST](filters-dynamic-manifest-rest-howto.md)
- [Azure Resource Manager-baserat REST-API](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Utforska Media Services [Azure CLI-referensdokumentationen](https://aka.ms/ams-v3-cli-ref) och titta på de här exemplen:

- [Skala mediereserverade enheter – CLI](media-reserved-units-cli-how-to.md)
- [Skapa ett Media Services-konto – CLI](./scripts/cli-create-account.md) 
- [Återställa autentiseringsuppgifter för konto – CLI](./scripts/cli-reset-account-credentials.md)
- [Skapa resurser – CLI](./scripts/cli-create-asset.md)
- [Ladda upp en fil – CLI](./scripts/cli-upload-file-asset.md)
- [Skapa transformeringar – CLI](./scripts/cli-create-transform.md)
- [Skapa jobb – CLI](./scripts/cli-create-jobs.md)
- [Skapa EventGrid – CLI](./scripts/cli-create-event-grid.md)
- [Publicera en resurs – CLI](./scripts/cli-publish-asset.md)
- [Filtrera – CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Utforska Media Services [.NET-referensdokumentationen](https://aka.ms/ams-v3-dotnet-ref) och titta på de här exemplen:

- [Självstudier: Ladda upp, koda och strömma videor – .NET](stream-files-tutorial-with-api.md) 
- [Självstudier: Strömma live med Media Services v3 – .NET](stream-live-tutorial-with-api.md)
- [Självstudier: Analysera videor med Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Skapa jobbindata från en lokal fil – .NET](job-input-from-local-file-how-to.md)
- [Skapa jobbindata från en HTTPS-URL – .NET](job-input-from-http-how-to.md)
- [Koda med en anpassad transformering – .NET](customize-encoder-presets-how-to.md)
- [Använda dynamisk AES-128-kryptering och tjänsten för nyckelleverans – .NET](protect-with-aes128.md)
- [Använda dynamisk DRM-kryptering och tjänsten för licensleverans – .NET](protect-with-drm.md)
- [Hämta en signeringsnyckel från den befintliga principen – .NET](get-content-key-policy-dotnet-howto.md)
- [Skapa filter med Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Avancerad exempel på video på begäran för Azure Functions v2 med Media Services v3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Granska Media Services [Java-referensdokumentationen](https://aka.ms/ams-v3-java-ref).

## <a name="nodejs"></a>Node.js

Utforska Media Services [Node.js-referensdokumentationen](https://aka.ms/ams-v3-nodejs-ref) och titta på de [exempel](https://github.com/Azure-Samples/media-services-v3-node-tutorials) som visar hur du använder Media Services API med node.js.

## <a name="python"></a>Python

Granska Media Services [Python-referensdokumentationen](https://aka.ms/ams-v3-python-ref).

## <a name="go"></a>Go

Granska Media Services [Go-referensdokumentationen](https://aka.ms/ams-v3-go-ref).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett konto – CLI](create-account-cli-how-to.md)
- [Använd API:er – CLI](access-api-cli-how-to.md)

