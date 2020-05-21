---
title: Vanliga SAS URI-problem och korrigeringar – Azure Marketplace
description: Vanliga problem påträffades och föreslagna lösningar när du arbetar med signaturer för delad åtkomst.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724606"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Vanliga SAS URI-problem och korrigeringar

Följande är vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| **Problem** | **Fel meddelande** | **Snabbkorrigering** |
| --------- | ------------------- | ------- |
| *Det gick inte att kopiera avbildningar* |  |  |
| "?" finns inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI med rekommenderade verktyg. |
| parametrarna "St" och "se" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med korrekta **start datum** och **slutdatum** -värden. |
| "SP = huvudwebbadress" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med behörigheter angivna som `Read` och `List` . |
| SAS-URI har tomma utrymmen i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n för att ta bort blank steg. |
| SAS-URI-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error.` | Granska och korrigera SAS URI-formatet. Återskapa vid behov. |
| SAS-URI: n "St" och "se"-parametrarna har ingen fullständig datum-och tids specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS-URI **start datum** och **slutdatum** parametrar ( `st` och `se` del strängar) måste ha ett fullständigt datum-och tids format, t. ex `11-02-2017T00:00:00Z` .. Förkortade versioner är ogiltiga (vissa kommandon i Azure CLI kan generera förkortade värden som standard). |
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
