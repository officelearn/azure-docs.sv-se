---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/11/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2018
ms.locfileid: "51616647"
---
## <a name="access-the-media-services-api"></a>Åtkomst till Media Services API

För att ansluta till Azure Media Services-API:er använder du autentisering av Azure AD-tjänstens huvudnamn. Följande kommando skapar ett Azure AD-program och bifogar ett huvudnamn för tjänsten till kontot. Du bör konfigurera ditt program med de värden som returneras.

Innan du kör skriptet måste du ersätta `amsaccount` och `amsResourceGroup` med de namn som du valde när du skapade dessa resurser. `amsaccount` är namnet på Azure Media Services-kontot där tjänstens huvudnamn ska bifogas.

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
