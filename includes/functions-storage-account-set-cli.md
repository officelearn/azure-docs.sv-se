---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 07780c94960c581ce69e4fdd45b346c75ab8b098
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329575"
---
### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

Öppna filen Local. Settings. JSON och kopiera värdet för `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Ange miljövariabeln `AZURE_STORAGE_CONNECTION_STRING` till anslutnings strängen med hjälp av detta bash-kommando:

```azurecli-interactive
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

När du ställer in anslutnings strängen i miljövariabeln `AZURE_STORAGE_CONNECTION_STRING`, kan du komma åt ditt lagrings konto utan att behöva tillhandahålla autentisering varje gång.