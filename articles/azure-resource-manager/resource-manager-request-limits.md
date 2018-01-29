---
title: "Azure Resource Manager-begärandebegränsningar | Microsoft Docs"
description: "Beskriver hur du använder begränsning med Azure Resource Manager begäranden när prenumerationsbegränsningar har uppnåtts."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc109cdaeade900e239624f408cea2a1f448ae5a
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="throttling-resource-manager-requests"></a>Begränsning av Resource Manager-begäranden
För varje prenumeration och klient Resource Manager gränser förfrågningar till 15 000 per timme Skriv- och läsbegäranden till 1 200 per timme. Dessa begränsningar gäller för varje instans av Azure Resource Manager. Det finns flera instanser i varje Azure-region och Azure Resource Manager distribueras till alla Azure-regioner.  Så i praktiken gränserna är effektivt mycket högre än dessa gränser, som användare hanteras begäranden vanligtvis av många olika instanser.

Om ditt program eller skript når dessa begränsningar, måste du begränsa dina önskemål. Den här artikeln visar hur du avgör den återstående begäranden som du har innan du når gränsen och hur du svarar när gränsen har uppnåtts.

När du når gränsen visas HTTP-statuskoden **429 för många begäranden**.

Antalet begäranden som har begränsats till din prenumeration eller din klient. Om du har flera samtidiga program som skickar begäranden i din prenumeration begäranden från dessa program läggs tillsammans för att fastställa antalet återstående begäranden.

Som omfattar-prenumerationsbegäranden är de som ID involve skicka din prenumeration, till exempel hämtar resursgrupper i din prenumeration. Klient omfång begäranden inkluderar inte ditt prenumerations-ID, till exempel hämtar giltig Azure platser.

## <a name="remaining-requests"></a>Återstående begäranden
Du kan bestämma antalet återstående begäranden genom att undersöka svarshuvuden. Varje begäran innehåller värden för antalet återstående Läs- och skrivbegäranden. I följande tabell beskrivs svarshuvuden som du kan undersöka för dessa värden:

| Svarshuvud | Beskrivning |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Prenumeration som omfattar läser återstående |
| x-ms-ratelimit-remaining-subscription-writes |Prenumeration som omfattar skriver återstående |
| x-ms-ratelimit-remaining-tenant-reads |Klient omfång läser återstående |
| x-ms-ratelimit-remaining-tenant-writes |Klient omfång skriver återstående |
| x-ms-ratelimit-remaining-subscription-resource-requests |Prenumerationen omfattas resurs typen begäranden kvar.<br /><br />Värdet i huvudet returneras bara om en tjänst har åsidosatt Standardgränsen. Resource Manager lägger till det här värdet i stället för prenumerationen läsning eller skrivning. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Prenumerationen omfattas resurs typen begäranden om diagnostikdatainsamling kvar.<br /><br />Värdet i huvudet returneras bara om en tjänst har åsidosatt Standardgränsen. Det här värdet visar antalet återstående begäranden om diagnostikdatainsamling (lista över resurser). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Klient omfång resurs typen begäranden kvar.<br /><br />Den här rubriken läggs endast för begäranden på klient-nivå och endast om en tjänst har åsidosatt Standardgränsen. Resource Manager lägger till det här värdet i stället för klient läsning eller skrivning. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Klient omfattas resurs typen begäranden om diagnostikdatainsamling kvar.<br /><br />Den här rubriken läggs endast för begäranden på klient-nivå och endast om en tjänst har åsidosatt Standardgränsen. |

## <a name="retrieving-the-header-values"></a>Hämtar värden i huvudet
Hämtar dessa värden i huvudet i din kod eller skript är inte annorlunda än att hämta alla huvudvärde. 

Till exempel i **C#**, du hämta huvudvärde från en **HttpWebResponse** objekt med namnet **svar** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell**, du hämta huvudets värde från ett Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Eller, om vill se begäranden för felsökning kan du ange den **-Debug** parameter på din **PowerShell** cmdlet.

```powershell
Get-AzureRmResourceGroup -Debug
```

Som returnerar flera värden, inklusive följande Svarsvärde:

```powershell
...
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 14999
...
```

I **Azure CLI**, du hämta huvudets värde med hjälp av alternativet mer utförlig.

```azurecli
az group list --verbose --debug
```

Som returnerar flera värden, inklusive följande objekt:

```azurecli
...
silly: returnObject
{
  "statusCode": 200,
  "header": {
    "cache-control": "no-cache",
    "pragma": "no-cache",
    "content-type": "application/json; charset=utf-8",
    "expires": "-1",
    "x-ms-ratelimit-remaining-subscription-reads": "14998",
    ...
```

## <a name="waiting-before-sending-next-request"></a>Väntar innan nästa begäran skickas
När du når gränsen för förfrågningar Resource Manager returnerar den **429** HTTP-statuskoden och en **försök igen efter** värdet i huvudet. Den **försök igen efter** värdet anger antalet sekunder som programmet ska vänta (eller strömsparläge) innan du skickar nästa begäran. Om du skickar en begäran innan återförsöksvärdet har gått ut, behandlas inte din begäran och ett nytt försök värde returneras.

## <a name="next-steps"></a>Nästa steg

* Mer information om begränsningar och kvoter finns [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md).
* Läs om hur du hanterar asynkrona REST-begäranden i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
