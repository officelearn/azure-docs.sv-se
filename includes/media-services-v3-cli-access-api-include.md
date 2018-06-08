---
title: ta med fil
description: ta med fil
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/29/2018
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 7454e96a2a05bf89a0455674a4f144534c374c71
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34667885"
---
## <a name="access-the-media-services-api"></a>Åtkomst till Media Services API

För att ansluta till Azure Media Services-API:er använder du autentisering av Azure AD-tjänstens huvudnamn. Följande kommando skapar ett Azure AD-program och bifogar ett huvudnamn för tjänsten till kontot. Du bör konfigurera ditt program med de värden som returneras.

Innan du kör skriptet måste du ersätta `amsaccount` och `amsResourceGroup` med de namn som du valde när du skapade dessa resurser. `amsaccount` är namnet på Azure Media Services-kontot där tjänstens huvudnamn ska bifogas.

Följande kommando returnerar `json`-utdata:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Det här kommandot genererar ett svar liknande följande:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Om du föredrar `xml` i svaret använder du följande kommando:

```azurecli-interactive
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
