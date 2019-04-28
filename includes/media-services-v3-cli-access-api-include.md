---
title: ta med fil
description: ta med fil
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309103"
---
## <a name="access-the-media-services-api"></a>Åtkomst till Media Services API

För att ansluta till Azure Media Services-API:er använder du autentisering av Azure AD-tjänstens huvudnamn. Följande kommando skapar ett Azure AD-program och bifogar ett huvudnamn för tjänsten till kontot. Du bör konfigurera ditt program med de värden som returneras.

Innan du kör skriptet måste du ersätta `amsaccount` och `amsResourceGroup` med de namn som du valde när du skapade dessa resurser. `amsaccount` är namnet på Azure Media Services-kontot där tjänstens huvudnamn ska bifogas.

Följande kommando returnerar `json`-utdata:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Det här kommandot genererar ett svar liknande följande:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Om du föredrar `xml` i svaret använder du följande kommando:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
