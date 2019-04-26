---
title: Gränser för begäran och begränsning, Azure Resource Manager
description: Beskriver hur du använder begränsningar med Azure Resource Manager-begäranden när prenumerationsbegränsningar har uppnåtts.
services: azure-resource-manager
documentationcenter: na
author: rockboyfor
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 03/05/2019
ms.date: 03/18/2019
ms.author: v-yeche
ms.custom: seodec18
ms.openlocfilehash: 91a776ba13ffaeeb4f8184371ae45a80d829ae46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389737"
---
# <a name="throttling-resource-manager-requests"></a>Begränsningsbegäranden Resource Manager

För varje Azure-prenumeration och en klient, Resource Manager kan upp till 12 000 läsa förfrågningar per timme och 1 200 skriva förfrågningar per timme. Dessa gränser är begränsade till huvudkontots ID gör begäranden och prenumerations-ID eller klient-ID. Om din begäran kommer från mer än en ägar-ID, är din gräns i en prenumeration eller -klient större än 12 000 och 1 200 per timme.

Begäranden som tillämpas på din prenumeration eller din klient. Prenumerationsbegäranden är sådana involve skicka din prenumeration ID, till exempel hämtar resursgrupper i prenumerationen. Klient-begäranden inkluderar inte ditt prenumerations-ID, till exempel hämta giltiga Azure-platser.

Dessa begränsningar gäller för varje Azure Resource Manager-instans. Det finns flera instanser i varje Azure-region och Azure Resource Manager distribueras till alla Azure-regioner.  Så i praktiken gränserna är effektivt mycket högre än de här gränserna, som användare är begäranden vanligtvis underhålls av många olika instanser.

Om ditt program eller skript når gränserna kan behöva du begränsa dina önskemål. Den här artikeln visar hur du fastställer de återstående begäranden som du har innan du når gränsen och hur du svarar när du har nått gränsen.

När du når gränsen kan du få HTTP-statuskoden **429 för många begäranden**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="remaining-requests"></a>Återstående begäranden
Du kan bestämma antalet återstående begäranden genom att undersöka svarshuvuden. Läsbegäranden returnera ett värde i rubriken för antalet återstående läsbegäranden. Skriva förfrågningar innehålla ett värde för antalet återstående skrivbegäranden. I följande tabell beskrivs de svarshuvuden som du kan undersöka för dessa värden:

| Svarshuvud | Beskrivning |
| --- | --- |
| x-MS-ratelimit-Remaining-Subscription-Reads |Prenumeration som omfattar läser återstående. Det här värdet returneras på läsåtgärder. |
| x-MS-ratelimit-Remaining-Subscription-Writes |Prenumeration som omfattar skriver återstående. Det här värdet returneras på skrivåtgärder. |
| x-MS-ratelimit-Remaining-tenant-Reads |Klient begränsad läser återstående |
| x-MS-ratelimit-Remaining-tenant-Writes |Klient begränsad skriver återstående |
| x-MS-ratelimit-Remaining-Subscription-Resource-Requests |Prenumeration begränsade typen resursbegäranden återstående.<br /><br />Det här rubrikvärdet returneras bara om en tjänst har åsidosatts Standardgränsen. Resource Manager lägger till det här värdet i stället för den prenumeration läsåtgärder eller skrivåtgärder. |
| x-MS-ratelimit-Remaining-Subscription-Resource-entities-Read |Prenumeration begränsade typen samling resursbegäranden återstående.<br /><br />Det här rubrikvärdet returneras bara om en tjänst har åsidosatts Standardgränsen. Det här värdet visar antalet begäranden om återstående (lista resurser). |
| x-MS-ratelimit-Remaining-tenant-Resource-Requests |Klient begränsade typen resursbegäranden återstående.<br /><br />Den här rubriken läggs endast för begäranden på klientnivån och endast om en tjänst har åsidosättas Standardgränsen. Resource Manager lägger till det här värdet i stället för den klient läsåtgärder eller skrivåtgärder. |
| x-MS-ratelimit-Remaining-tenant-Resource-entities-Read |Klient begränsade typen samling resursbegäranden återstående.<br /><br />Den här rubriken läggs endast för begäranden på klientnivån och endast om en tjänst har åsidosättas Standardgränsen. |

## <a name="retrieving-the-header-values"></a>Hämta huvudvärden
Hämtar dessa värden i huvudet i din kod eller skript skiljer än att hämta alla huvudets värde. 

Till exempel i **C#**, du hämta huvudvärde från en **HttpWebResponse** objekt med namnet **svar** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell**, du hämta huvudets värde från en Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.chinacloudapi.cn/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

En fullständig PowerShell-exempel finns i [Kontrollera Resource Manager-gränserna för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Om du vill se begäranden för felsökning kan du ange den **-felsöka** parametern på din **PowerShell** cmdlet.

```powershell
Get-AzResourceGroup -Debug
```

Som returnerar flera värden, inklusive följande Svarsvärde:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Använd en skrivåtgärd för att få skrivning gränser kan: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location chinanorth -Debug
```

Som returnerar flera värden, inklusive följande värden:

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

I **Azure CLI**, du hämta huvudets värde med hjälp av alternativet mer utförlig.

```azurecli
az group list --verbose --debug
```

Som returnerar flera värden, inklusive följande värden:

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Använd en skrivåtgärd för att få skrivning gränser kan: 

```azurecli
az group create -n myresourcegroup --location chinanorth --verbose --debug
```

Som returnerar flera värden, inklusive följande värden:

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="waiting-before-sending-next-request"></a>Att vänta innan nästa begäran skickas
När du når gränsen för förfrågningar Resource Manager returnerar den **429** HTTP-statuskoden och en **Retry-After** värdet i huvudet. Den **Retry-After** värdet anger hur många sekunder som programmet bör vänta (eller strömsparläge) innan nästa förfrågan skickas. Om du skickar en begäran innan återförsöksvärdet har gått ut kan bearbeta inte din begäran och ett nytt försök värde returneras.

## <a name="next-steps"></a>Nästa steg

* En fullständig PowerShell-exempel finns i [Kontrollera Resource Manager-gränserna för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Mer information om begränsningar och kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
* Läs om hur du hanterar asynkrona REST-begäranden i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).

<!--Update_Description: update meta properties, update cmdlet -->