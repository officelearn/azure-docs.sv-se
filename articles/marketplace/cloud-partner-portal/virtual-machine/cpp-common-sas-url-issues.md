---
title: Vanliga SAS-URL-problem och korrigeringar för Azure Marketplace
description: Lista vanliga problem med att använda uri:er för signatur med delad åtkomst och möjliga lösningar.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: c575389538230218e1e6e4f172ebcfbee8ee51dc
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273468"
---
# <a name="common-sas-url-issues-and-fixes"></a>Vanliga problem med SAS-URL-meddelanden och korrigeringar

> [!IMPORTANT]
> Från och med den 13 april 2020 börjar vi flytta hanteringen av dina Azure Virtual Machine-erbjudanden till Partner Center. Efter migreringen skapar och hanterar du dina erbjudanden i Partner center. Följ instruktionerna i [vanliga SAS-URL-problem och korrigeringar](https://aka.ms/AzureSAS_URL_FAQ) för att hantera dina migrerade erbjudanden.

I följande tabell visas några av de vanligaste problemen när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hårddiskarna för din lösning), tillsammans med föreslagna lösningar.

| **Problem** | **Meddelande om fel** | **Snabbkorrigering** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Det gick inte att kopiera bilder* |  |  |
| "?" finns inte i SAS-URL:en | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL:en med rekommenderade verktyg. |
| "st" och "se" parametrar som inte finns i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL:en med rätt **startdatum** och **slutdatumvärden** i den. | 
| "sp=rl" inte i SAS-URL | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Uppdatera SAS-URL:en med `Read` `List`behörigheter som och . | 
| SAS-URL:en har blanksteg i VHD-namn | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Uppdatera SAS-URL:en för att ta bort blanksteg. |
| Auktoriseringsfel för SAS-URL | `Failure: Copying Images. Not able to download blob due to authorization error` | Granska och korrigera SAS URI-format. Regenerera vid behov. |
| SAS URL "st" och "se" parametrar har inte fullständig datum-tid specifikation | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | PARAMETRAR för SAS-URL-startdatum och **slutdatum** `st` (och `se` delsträngar) måste `11-02-2017T00:00:00Z`ha fullständigt datetime-format, till exempel . **Start Date** Förkortade versioner är ogiltiga. (Vissa kommandon i Azure CLI kan generera förkortade värden som standard.) | 
|  |  |  |

Mer information finns i [Använda SIGNATURER för delad åtkomst (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
