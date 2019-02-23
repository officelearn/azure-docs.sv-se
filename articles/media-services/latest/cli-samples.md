---
title: Azure CLI-exempel – Azure Media Services | Microsoft Docs
description: Azure CLI-exempel för Azure Media Services-tjänsten
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/21/2019
ms.author: juliako
ms.openlocfilehash: bbd57933993e22dd32b84f1d44175bb3b3d749c9
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56672434"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure CLI-exempel för Azure Media Services

I följande tabell innehåller länkar till Azure CLI-exempel för Azure Media Services.

## <a name="examples"></a>Exempel

|  |  |
|---|---|
|**Skalning**||
| [Skala Mediereserverade enheter](media-reserved-units-cli-how-to.md)|För analys av ljud och Video Analysis jobb som utlöses av Media Services v3 eller Video Indexer kan rekommenderar vi starkt att etablera ditt konto med 10 S3 MRUs. <br/>Skriptet visar hur du använder CLI för att skala Mediereserverade enheter (MRUs).|
|**Konto**||
| [Skapa ett Media Services-konto](create-account-cli-how-to.md) | Skapar ett Azure Media Services-konto. Dessutom skapar en tjänst huvudnamn som kan användas för åtkomst till API: er för att programmässigt hantera kontot. |
| [Återställ autentiseringsuppgifter](./scripts/cli-reset-account-credentials.md)|Återställer autentiseringsuppgifterna för ditt konto och hämtar app.config inställningarna igen.|
|**Tillgångar**||
| [Skapa tillgångar](./scripts/cli-create-asset.md)|Skapar en tillgång med Media Services för att överföra innehåll till.|
| [Ladda upp en fil](./scripts/cli-upload-file-asset.md)|Överför en lokal fil till en lagringsbehållare.|
| **Omvandlar** och **jobb**||
| [Skapa transformeringar](./scripts/cli-create-transform.md)|Visar hur du skapar transformeringar. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”).<br/> Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. I annat fall återanvända den. |
| [Skapa jobb](./scripts/cli-create-jobs.md)|Skickar ett jobb för att en enkel kodning transformering med hjälp av HTTPs-URL.|
| [Skapa EventGrid](./scripts/cli-create-event-grid.md)|Skapar ett konto på Event Grid-prenumeration för tillståndsändringar för jobbet.|
| **Deliver**||
| [Publicera en tillgång](./scripts/cli-publish-asset.md)| Skapar en Strömningspositionerare och hämtar tillbaka strömmande URL: er. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Konfigurerar ett filter för en Video på begäran tillgång och visar hur du använder CLI för att skapa [kontofilter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) och [tillgången filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Se också

- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
- [Snabbstart: Stream videofiler – CLI](stream-files-cli-quickstart.md)
