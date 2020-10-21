---
title: Virtuella dator SAS-felmeddelanden – Azure Marketplace
description: Vanliga frågor och svar när du arbetar med signaturer för delad åtkomst (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 7b91cf3d49d88501fb908d157892ec3eb24bee6e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284536"
---
# <a name="virtual-machine-sas-failure-messages"></a>SAS-felmeddelanden för virtuella datorer

Följande är vanliga problem som uppstår när du arbetar med signaturer för delad åtkomst (som används för att identifiera och dela de uppladdade virtuella hård diskarna för din lösning), tillsammans med föreslagna lösningar.

| Problem | Fel meddelande | Åtgärda |
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

## <a name="next-steps"></a>Nästa steg

- [Generera SAS-URI](azure-vm-get-sas-uri.md)
