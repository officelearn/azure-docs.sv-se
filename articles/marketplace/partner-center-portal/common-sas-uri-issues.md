---
title: Vanliga SAS URI-problem och korrigeringar – Azure Marketplace
description: Vanliga problem påträffades och föreslagna lösningar när du arbetar med signaturer för delad åtkomst.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266245"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Vanliga SAS URI-problem och korrigeringar

> [!IMPORTANT]
> Vi flyttar hanteringen av din virtuella Azure-dator med erbjudanden från Cloud Partner Portal till Partner Center. Innan du har migrerat dina erbjudanden följer du anvisningarna i [vanliga SAS URI-problem och korrigeringar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) för Cloud Partner Portal för att hantera dina erbjudanden.

Följande är vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| **Problem** | **Fel meddelande** | **Snabbkorrigering** |
| --------- | ------------------- | ------- |
| *Det gick inte att kopiera avbildningar* |  |  |
| "?" finns inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI med rekommenderade verktyg. |
| parametrarna "St" och "se" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med korrekta **start datum** och **slutdatum** -värden. |
| "SP = huvudwebbadress" inte i SAS-URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n med behörigheter `Read` angivna `List`som och. |
| SAS-URI har tomma utrymmen i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URI: n för att ta bort blank steg. |
| SAS-URI-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error.` | Granska och korrigera SAS URI-formatet. Återskapa vid behov. |
| SAS-URI: n "St" och "se"-parametrarna har ingen fullständig datum-och tids specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | SAS-URI **start datum** och **slutdatum** parametrar`st` ( `se` och del strängar) måste ha ett fullständigt datum-och tids format, `11-02-2017T00:00:00Z`t. ex.. Förkortade versioner är ogiltiga (vissa kommandon i Azure CLI kan generera förkortade värden som standard). |
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
