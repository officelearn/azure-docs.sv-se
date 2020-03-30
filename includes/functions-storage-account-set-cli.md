---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4ace70abe0112e0fe27d177c02bcb697746c92cc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "78262228"
---
### <a name="set-the-storage-account-connection"></a>Ange anslutning till lagringskonto

Öppna filen local.settings.json och kopiera `AzureWebJobsStorage`värdet för , som är anslutningssträngen för lagringskonto. Ställ `AZURE_STORAGE_CONNECTION_STRING` in miljövariabeln på anslutningssträngen med det här bash-kommandot:

```bash
AZURE_STORAGE_CONNECTION_STRING="<STORAGE_CONNECTION_STRING>"
```

När du ställer in `AZURE_STORAGE_CONNECTION_STRING` anslutningssträngen i miljövariabeln kan du komma åt ditt Lagringskonto utan att behöva ange autentisering varje gång.