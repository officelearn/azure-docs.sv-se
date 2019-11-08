---
title: Vanliga SAS URL-problem och korrigeringar för Azure Marketplace
description: Lista vanliga problem med att använda signaturer för signaturer för delad åtkomst och möjliga lösningar.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 09/27/2018
ms.author: pabutler
ms.openlocfilehash: 502ba1a65f9b0740a51c7a4da219cc87af494f27
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813318"
---
# <a name="common-sas-url-issues-and-fixes"></a>Vanliga SAS URL-problem och korrigeringar

I följande tabell visas några vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| **Ge** | **Fel meddelande** | **Löser** | 
| --------- | ------------------- | ------- | 
| &emsp;*inte att kopiera bilder* |  |  |
| "?" finns inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en med rekommenderade verktyg. |
| parametrarna "St" och "se" inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en med rätt **start datum** och **slutdatum** -värden. | 
| "SP = huvudwebbadress" inte i SAS-webbadress | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Uppdatera SAS-URL: en med behörigheter som har angetts som `Read` och `List`. | 
| SAS-URL: en har tomma utrymmen i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en för att ta bort blank steg. |
| URL-auktoriseringsfel för SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Granska och korrigera SAS URI-formatet. Återskapa vid behov. |
| SAS-URL: erna "St" och "se" har inte fullständig datum-tid-specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | URL: er för SAS-URL **start datum** och **slutdatum** (`st`-och `se` under strängar) måste ha fullständigt datetime-format, till exempel `11-02-2017T00:00:00Z`. Förkortade versioner är inte giltiga. (Vissa kommandon i Azure CLI kan skapa korta värden som standard.) | 
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
