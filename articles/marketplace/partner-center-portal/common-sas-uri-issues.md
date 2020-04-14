---
title: Vanliga SAS URI-problem och korrigeringar – Azure Marketplace
description: Vanliga problem som uppstått och föreslog lösningar när du arbetar med signaturer för delad åtkomst.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266245"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Vanliga SAS URI-problem och korrigeringar

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure Virtual Machine-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [vanliga SAS URI-problem och korrigeringar](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) för Cloud Partner Portal för att hantera dina erbjudanden.

Här följer vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hårddiskarna för din lösning), tillsammans med föreslagna lösningar.

| **Problem** | **Meddelande om fel** | **Snabbkorrigering** |
| --------- | ------------------- | ------- |
| *Det gick inte att kopiera bilder* |  |  |
| "?" finns inte i SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS URI med rekommenderade verktyg. |
| "st" och "se" parametrar som inte finns i SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS URI med rätt **startdatum** och **slutdatumvärden.** |
| "sp=rl" inte i SAS URI | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS URI med behörigheter som `Read` och `List`. |
| SAS URI har blanksteg i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS URI för att ta bort blanksteg. |
| SAS URI-auktoriseringsfel | `Failure: Copying Images. Not able to download blob due to authorization error.` | Granska och korrigera SAS URI-format. Regenerera vid behov. |
| SAS URI "st" och "se" parametrar har inte fullständig datumtidsspecifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | PARAMETRAR för SAS URI **Startdatum** och **Slutdatum** (och`st` `se` delsträngar) måste `11-02-2017T00:00:00Z`ha ett fullständigt datumformat, till exempel . Förkortade versioner är ogiltiga (vissa kommandon i Azure CLI kan generera förkortade värden som standard). |
|  |  |  |

Mer information finns i [Använda SIGNATURER för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
