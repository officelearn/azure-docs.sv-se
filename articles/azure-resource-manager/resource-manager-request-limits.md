---
title: Begär ande gränser och begränsning
description: Beskriver hur du använder begränsningar med Azure Resource Manager-begäranden när prenumerationsbegränsningar har uppnåtts.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: b47943d69d209f5a0406c293b5a24c6ac0ad0c10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150465"
---
# <a name="throttling-resource-manager-requests"></a>Begränsningsbegäranden Resource Manager

I den här artikeln beskrivs hur Azure Resource Manager begränsnings begär Anden. Det visar hur du spårar antalet begär Anden som återstår innan gränsen nås och hur du svarar när du har nått gränsen.

Begränsning sker på två nivåer. Azure Resource Manager begränsar begär Anden för prenumerationen och klienten. Om begäran är under begränsnings gränserna för prenumerationen och klient organisationen dirigerar Resource Manager begäran till resurs leverantören. Resurs leverantören tillämpar begränsnings begränsningar som är skräddarsydda för dess åtgärder. Följande bild visar hur begränsningen används som en begäran går från användaren till Azure Resource Manager och resurs leverantören.

![Begär begränsning](./media/resource-manager-request-limits/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Prenumerations-och klient gränser

Alla åtgärder på prenumerations nivå och klient nivå är föremål för begränsning av gränser. Prenumerations förfrågningar är sådana som innebär att du skickar ditt prenumerations-ID, till exempel att hämta resurs grupperna i din prenumeration. Klient-begäranden inkluderar inte ditt prenumerations-ID, till exempel hämta giltiga Azure-platser.

Standard begränsningarna för begränsning per timme visas i följande tabell.

| Omfång | Åtgärder | Gräns |
| ----- | ---------- | ------- |
| Prenumeration | läsoperationer | 12000 |
| Prenumeration | text | 15 000 |
| Prenumeration | skriver | 1200 |
| Klientorganisation | läsoperationer | 12000 |
| Klientorganisation | skriver | 1200 |

De här gränserna gäller det säkerhetsobjekt (användare eller program) som skickar förfrågningarna och prenumerationens eller klientorganisationens ID. Om dina förfrågningar kommer från fler än ett säkerhetsobjekt är begränsningen för prenumerationen eller klientorganisationen högre än 12 000 respektive 1 200 per timme.

Dessa begränsningar gäller för varje Azure Resource Manager-instans. Det finns flera instanser i varje Azure-region och Azure Resource Manager distribueras till alla Azure-regioner.  I praktiken är gränserna högre än dessa gränser. Begär Anden från en användare hanteras vanligt vis av olika instanser av Azure Resource Manager.

## <a name="resource-provider-limits"></a>Resource Provider-gränser

Resurs leverantörer tillämpar egna begränsnings gränser. Eftersom resurs hanterarens begränsningar efter ägar-ID och en instans av Resource Manager, kan det hända att resurs leverantören får fler förfrågningar än standard gränserna i föregående avsnitt.

I det här avsnittet beskrivs begränsnings gränserna för vissa vanliga resurs leverantörer.

### <a name="storage-throttling"></a>Lagrings begränsning

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Nätverks begränsning

Microsoft. Network Resource-providern tillämpar följande begränsningar:

| Åtgärd | Gräns |
| --------- | ----- |
| Skriv/ta bort (placering) | 1000 per 5 minuter |
| läsa (Hämta) | 10000 per 5 minuter |

### <a name="compute-throttling"></a>Beräknings begränsning

Information om hur du begränsar gränserna för beräknings åtgärder finns i [FELSÖKA API begränsnings fel – beräkna](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Använd [Virtual Machine Scale Sets åtgärder](/rest/api/compute/virtualmachinescalesetvms)för att kontrol lera virtuella dator instanser i en skalnings uppsättning för virtuella datorer. Använd till exempel [virtuella datorer för skalnings uppsättning för virtuella datorer – lista](/rest/api/compute/virtualmachinescalesetvms/list) med parametrar för att kontrol lera energi läget för virtuella dator instanser. Detta API minskar antalet begär Anden.

### <a name="azure-resource-graph-throttling"></a>Begränsning av Azure-resurs diagram

Azure Resource Graph begränsar antalet begär anden till åtgärder. Stegen i den här artikeln för att fastställa de återstående förfrågningarna och hur du svarar när gränsen nås gäller även för resurs diagram. Resurs diagram anger dock sin egen gräns och återställnings takt. Mer information finns i [Begränsningar i Azure Resource Graph](../governance/resource-graph/overview.md#throttling).

## <a name="request-increase"></a>Begär ökning

Ibland kan begränsnings begränsningar ökas. Om du vill se om begränsnings gränserna för ditt scenario kan ökas, skapar du en support förfrågan. Information om ditt anrops mönster kommer att utvärderas.

## <a name="error-code"></a>Felkod

När du når gränsen kan du få HTTP-statuskoden **429 för många begäranden**. Svaret innehåller ett värde för **återförsök efter** , som anger antalet sekunder som programmet ska vänta (eller vila) innan nästa förfrågan skickas. Om du skickar en begäran innan återförsöksvärdet har gått ut kan bearbeta inte din begäran och ett nytt försök värde returneras.

När du har väntat den angivna tiden kan du också stänga och öppna anslutningen till Azure igen. Genom att återställa anslutningen kan du ansluta till en annan instans av Azure Resource Manager.

Om du använder en Azure SDK kan SDK: n ha en automatisk omförsöks konfiguration. Mer information finns i [vägledning för återförsök för Azure-tjänster](/azure/architecture/best-practices/retry-service-specific).

Vissa resurs leverantörer returnerar 429 för att rapportera ett tillfälligt problem. Problemet kan vara ett överbelastnings tillstånd som inte direkt orsakas av din begäran. Eller så kan det representera ett tillfälligt fel meddelande om status för mål resursen eller beroende resursen. Nätverks resurs leverantören returnerar till exempel 429 med **RetryableErrorDueToAnotherOperation** -felkoden när mål resursen är låst av en annan åtgärd. Ta reda på om felet kommer från begränsning eller ett tillfälligt tillstånd genom att visa fel informationen i svaret.

## <a name="remaining-requests"></a>Återstående begäranden

Du kan bestämma antalet återstående begäranden genom att undersöka svarshuvuden. Läs begär Anden returnerar ett värde i rubriken för antalet återstående Läs begär Anden. Skriv förfrågningar innehåller ett värde för antalet återstående Skriv förfrågningar. I följande tabell beskrivs de svarshuvuden som du kan undersöka för dessa värden:

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

Resurs leverantören kan även returnera svarshuvuden med information om återstående förfrågningar. Information om svarshuvuden som returneras av Compute Resource providern finns i svarshuvuden för [anrops frekvens information](../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Hämta huvudvärden

Hämtar dessa värden i huvudet i din kod eller skript skiljer än att hämta alla huvudets värde. 

Till exempel i **C#** , du hämta huvudvärde från en **HttpWebResponse** objekt med namnet **svar** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell**, du hämta huvudets värde från en Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
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
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
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
az group create -n myresourcegroup --location westus --verbose --debug
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

## <a name="next-steps"></a>Nästa steg

* En fullständig PowerShell-exempel finns i [Kontrollera Resource Manager-gränserna för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Mer information om begränsningar och kvoter finns i [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).
* Läs om hur du hanterar asynkrona REST-begäranden i [spåra asynkrona åtgärder i Azure](resource-manager-async-operations.md).
