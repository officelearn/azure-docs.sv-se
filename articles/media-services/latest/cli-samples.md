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
ms.custom: ''
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 5559f9055da3a2a852427c0f27d367159cdc7655
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49388518"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure CLI-exempel för Azure Media Services

I följande tabell innehåller länkar till Azure CLI-exempel för Azure Media Services.

|  |  |
|---|---|
|**Konto**||
| [Skapa ett Media Services-konto](./scripts/cli-create-account.md) | Skapar ett Azure Media Services-konto. Dessutom skapar en tjänst huvudnamn som kan användas för åtkomst till API: er för att programmässigt hantera kontot. |
| [Återställ autentiseringsuppgifter](./scripts/cli-reset-account-credentials.md)|Återställer autentiseringsuppgifterna för ditt konto och hämtar app.config inställningarna igen.|
|**Tillgångar**||
| [Skapa tillgångar](./scripts/cli-create-asset.md)|Skapar en tillgång med Media Services för att överföra innehåll till.|
| [Ladda upp en fil](./scripts/cli-upload-file-asset.md)|Överför en lokal fil till en lagringsbehållare.|
| [Publicera en tillgång](./scripts/cli-publish-asset.md)| Skapar en Strömningspositionerare och hämtar tillbaka strömmande URL: er. |
| **Omvandlar** och **jobb**||
| [Skapa transformeringar](./scripts/cli-create-transform.md)|Visar hur du skapar transformeringar. Transformeringar beskriver ett enkelt arbetsflöde av uppgifter för att bearbeta video- eller ljudfiler (kallas ofta ”recept”).<br/> Du bör alltid kontrollera om det redan finns en transformering med önskat namn och ”recept”. I annat fall återanvända den. |
| [Skapa jobb](./scripts/cli-create-jobs.md)|Skickar ett jobb för att en enkel kodning transformering med hjälp av HTTPs-URL.|
| [Skapa EventGrid](./scripts/cli-create-event-grid.md)|Skapar ett konto på Event Grid-prenumeration för tillståndsändringar för jobbet.|

## <a name="see-also"></a>Se också

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
