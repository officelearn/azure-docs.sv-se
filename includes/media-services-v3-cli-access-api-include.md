---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149213"
---
## <a name="access-the-media-services-api"></a>Åtkomst till Media Services API

För att ansluta till Azure Media Services-API:er använder du autentisering av Azure AD-tjänstens huvudnamn. Följande kommando skapar ett Azure AD-program och bifogar ett huvudnamn för tjänsten till kontot. Du bör konfigurera ditt program med de värden som returneras.

Innan du kör skriptet, bör du ersätta den `amsaccount` och `amsResourceGroup` med namn som du valde när du skapar dessa resurser. `amsaccount` är namnet på Azure Media Services-kontot där tjänstens huvudnamn ska bifogas.

Följande kommando returnerar `json`-utdata:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Det här kommandot genererar ett svar liknande följande:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Om du föredrar `xml` i svaret använder du följande kommando:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
