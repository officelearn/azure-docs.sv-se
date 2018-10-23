---
title: Vanliga problem för SAS-URL och korrigeringar för Azure Marketplace | Microsoft Docs
description: 'Lista över vanliga problem när det gäller med signatur för delad åtkomst URI: er och möjliga lösningar.'
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/27/2018
ms.author: pbutlerm
ms.openlocfilehash: b20b1506dfcd32ea7d5bfca0847393d1652afb78
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639996"
---
# <a name="common-sas-url-issues-and-fixes"></a>Vanliga problem för SAS-URL och korrigeringar

I följande tabell visas några av de vanliga problem som kan uppstå när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de överförda virtuella hårddiskarna för din lösning), tillsammans med förslag på lösningar.

| **Problemet** | **Meddelande om anslutningsfel** | **Åtgärda** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Det gick inte att kopiera avbildningar* |  |  |  |
| ””? hittades inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL med rekommenderade verktyg. |
| parametrarna för ”st” och ”se” inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-Webbadressen med rätt **startdatum** och **slutdatum** värden i den. | 
| ”sp = rl” inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Uppdatera SAS-Webbadressen med behörigheterna inställda som `Read` och `List`. | 
| SAS-URL har blanksteg i namn på virtuell Hårddisk | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-Webbadressen för att ta bort blanksteg. |
| SAS-URL-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error` | Granska och korrigera SAS-URI-format. Återskapa om det behövs. |
| SAS-URL ”a” och ”se” parametrar har inte fullständig datum / tid-specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS-Webbadressen **startdatum** och **slutdatum** parametrar (`st` och` se` delsträngar) måste ha fullständig datetime-format, till exempel `11-02-2017T00:00:00Z`. Förkortade versioner är inte giltig. (Vissa kommandon i Azure CLI kan generera förkortade värden som standard.) | 
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
