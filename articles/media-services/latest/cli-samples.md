---
title: Azure CLI - exempel i Azure Media Services | Microsoft Docs
description: Azure CLI-exempel för Azure Media Services-tjänsten
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 04/15/2018
ms.author: juliako
ms.openlocfilehash: bbf69bdcc92316642f6b37d267cdea2aad920316
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Azure CLI-exempel för Azure Media Services

Följande tabell innehåller länkar till exempel Azure CLI för Azure Media Services.

|  |  |
|---|---|
|**Konto**||
| [Skapa ett Media Services-konto](./scripts/cli-create-account.md) | Skapar ett Azure Media Services-konto. Dessutom skapar en tjänst UPN som kan användas för åtkomst till API: er för att hantera kontot programmässigt. |
| [Återställa autentiseringsuppgifter](./scripts/cli-reset-account-credentials.md)|Återställer autentiseringsuppgifterna för ditt konto och hämtar app.config inställningarna igen.|
|**Tillgångar**||
| [Skapa tillgångar](./scripts/cli-create-asset.md)|Skapar en tillgång med Media Services för att överföra innehåll till.|
| [Överför en fil](./scripts/cli-upload-file-asset.md)|Överför en lokal fil till en lagringsbehållare.|
| [Publicera en tillgång](./scripts/cli-publish-asset.md)| Skapar en Strömningspositionerare och hämtar tillbaka strömning URL: er. |
| **Transformerar** och **jobb**||
| [Skapa transformeringar](./scripts/cli-create-transform.md)|Visar hur du skapar transformeringar. Transformeringar beskrivs ett enkelt arbetsflöde för aktiviteter för att bearbeta video eller ljud-filer (kallas ofta ”recept”).<br/> Du bör alltid kontrollera om en transformering med önskat namn och ”recept” finns redan. Om den finns, återanvända den. |
| [Skapa jobb](./scripts/cli-create-jobs.md)|Skickar ett jobb till en enkel kodning transformering med hjälp av HTTPs-URL.|
| [Skapa EventGrid](./scripts/cli-create-event-grid.md)|Skapar ett konto nivån händelse rutnätet prenumeration för jobbet tillstånd ändras.|

