---
title: Vanliga SAS URL-problem och korrigeringar för Azure Marketplace
description: Lista vanliga problem med att använda signaturer för signaturer för delad åtkomst och möjliga lösningar.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147052"
---
# <a name="common-sas-url-issues-and-fixes"></a>Vanliga SAS URL-problem och korrigeringar

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [vanliga SAS-URL-problem och korrigeringar](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) för att hantera dina migrerade erbjudanden.

I följande tabell visas några vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| **Problem** | **Fel meddelande** | **Snabbkorrigering** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Det gick inte att kopiera avbildningar* |  |  |
| "?" finns inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en med rekommenderade verktyg. |
| parametrarna "St" och "se" inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en med rätt **start datum** och **slutdatum** -värden. | 
| "SP = huvudwebbadress" inte i SAS-webbadress | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Uppdatera SAS-URL: en med behörigheter `Read` som `List`angetts som och. | 
| SAS-URL: en har tomma utrymmen i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL: en för att ta bort blank steg. |
| URL-auktoriseringsfel för SAS | `Failure: Copying Images. Not able to download blob due to authorization error` | Granska och korrigera SAS URI-formatet. Återskapa vid behov. |
| SAS-URL: erna "St" och "se" har inte fullständig datum-tid-specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | SAS-URL **start datum** och **slutdatum** parametrar`st` ( `se` och del strängar) måste ha fullständigt datetime-format, till exempel `11-02-2017T00:00:00Z`. Förkortade versioner är inte giltiga. (Vissa kommandon i Azure CLI kan skapa korta värden som standard.) | 
|  |  |  |

Mer information finns i [använda signaturer för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
