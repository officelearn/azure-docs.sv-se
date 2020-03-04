---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262228"
---
### <a name="set-the-storage-account-connection"></a>Ange lagrings konto anslutningen

Öppna filen Local. Settings. JSON och kopiera värdet för `AzureWebJobsStorage`, vilket är anslutnings strängen för lagrings kontot. Ange den `AZURE_STORAGE_CONNECTION_STRING` miljövariabeln till anslutnings strängen genom att använda det här bash-kommandot:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

När du anger anslutnings strängen i `AZURE_STORAGE_CONNECTION_STRING`-miljövariabeln, kan du komma åt ditt lagrings konto utan att behöva tillhandahålla autentisering varje gång.