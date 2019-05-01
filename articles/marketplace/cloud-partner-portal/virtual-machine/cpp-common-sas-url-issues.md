---
title: Vanliga problem för SAS-URL och korrigeringar för Azure Marketplace
description: 'Lista över vanliga problem när det gäller med signatur för delad åtkomst URI: er och möjliga lösningar.'
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 4f2770312624e1ca4c939ade458a451eb03f9d20
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938382"
---
# <a name="common-sas-url-issues-and-fixes"></a>Vanliga problem för SAS-URL och korrigeringar

I följande tabell visas några av de vanliga problem som kan uppstå när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de överförda virtuella hårddiskarna för din lösning), tillsammans med förslag på lösningar.

| **Problemet** | **Meddelande om anslutningsfel** | **Fix** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Det gick inte att kopiera avbildningar* |  |  |
| ””? hittades inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL med rekommenderade verktyg. |
| parametrarna för ”st” och ”se” inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-Webbadressen med rätt **startdatum** och **slutdatum** värden i den. | 
| ”sp = rl” inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Uppdatera SAS-Webbadressen med behörigheterna inställda som `Read` och `List`. | 
| SAS-URL har blanksteg i namn på virtuell Hårddisk | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-Webbadressen för att ta bort blanksteg. |
| SAS-URL-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error` | Granska och korrigera SAS-URI-format. Återskapa om det behövs. |
| SAS-URL ”a” och ”se” parametrar har inte fullständig datum / tid-specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS-Webbadressen **startdatum** och **slutdatum** parametrar (`st` och `se` delsträngar) måste ha fullständig datetime-format, till exempel `11-02-2017T00:00:00Z`. Förkortade versioner är inte giltig. (Vissa kommandon i Azure CLI kan generera förkortade värden som standard.) | 
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
