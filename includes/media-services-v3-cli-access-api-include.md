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
ms.openlocfilehash: 58e9156091702718dccd75eb4a57e5b6d8c1f073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896324"
---
## <a name="access-the-media-services-api"></a>Åtkomst till Media Services-API:n

För att ansluta till Azure Media Services-API:er använder du autentisering av Azure AD-tjänstens huvudnamn. Följande kommando skapar ett Azure AD-program och bifogar ett huvudnamn för tjänsten till kontot. Du bör konfigurera ditt program med de värden som returneras.

Innan du kör skriptet bör du ersätta `amsaccount` och `amsResourceGroup` med de namn som du valde när du skapade resurserna. `amsaccount` är namnet på Azure Media Services-kontot där tjänstens huvudnamn ska bifogas.

Om du har åtkomst till flera prenumerationer ställer du först in den aktiva prenumerationen på den prenumeration där Media Services kontot skapades.

```azurecli
az account set --subscription subscriptionId
```

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
