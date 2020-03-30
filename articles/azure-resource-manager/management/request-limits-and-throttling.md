---
title: Begärandebegränsningar och begränsningar
description: Beskriver hur du använder begränsning med Azure Resource Manager-begäranden när prenumerationsgränser har uppnåtts.
ms.topic: conceptual
ms.date: 03/24/2020
ms.custom: seodec18
ms.openlocfilehash: 4d387749261747eb9ea1ea26629ade4fe8729856
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239366"
---
# <a name="throttling-resource-manager-requests"></a>Begränsning av Resource Manager-förfrågningar

I den här artikeln beskrivs hur Begäranden i Azure Resource Manager begränsar. Den visar hur du spårar antalet begäranden som finns kvar innan du når gränsen och hur du ska svara när du har nått gränsen.

Begränsning sker på två nivåer. Azure Resource Manager begränsar begäranden för prenumerationen och klienten. Om begäran är under begränsningsgränserna för prenumerationen och klienten dirigerar Resource Manager begäran till resursleverantören. Resursprovidern tillämpar begränsningsgränser som är anpassade till dess verksamhet. Följande avbildning visar hur begränsning tillämpas som en begäran går från användaren till Azure Resource Manager och resursleverantören.

![Begär begränsning](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Prenumerations- och klientgränser

Varje åtgärd på prenumerationsnivå och klientnivå är föremål för begränsningsgränser. Prenumerationsbegäranden är sådana som innebär att ditt prenumerations-ID måste skickas, till exempel hämta resursgrupperna i din prenumeration. Klientbegäranden inkluderar inte ditt prenumerations-ID, till exempel hämtning av giltiga Azure-platser.

Standardbegränsningsgränserna per timme visas i följande tabell.

| Omfång | Åtgärder | Gräns |
| ----- | ---------- | ------- |
| Prenumeration | Läser | 12000 |
| Prenumeration | Tar bort | 15 000 |
| Prenumeration | Skriver | 1200 |
| Klientorganisation | Läser | 12000 |
| Klientorganisation | Skriver | 1200 |

De här gränserna gäller det säkerhetsobjekt (användare eller program) som skickar förfrågningarna och prenumerationens eller klientorganisationens ID. Om dina förfrågningar kommer från fler än ett säkerhetsobjekt är begränsningen för prenumerationen eller klientorganisationen högre än 12 000 respektive 1 200 per timme.

Dessa gränser gäller för varje Azure Resource Manager-instans. Det finns flera instanser i alla Azure-regioner och Azure Resource Manager distribueras till alla Azure-regioner.  Så i praktiken är gränserna högre än dessa gränser. Begäranden från en användare hanteras vanligtvis av olika instanser av Azure Resource Manager.

## <a name="resource-provider-limits"></a>Begränsningar för resursprovider

Resursleverantörer tillämpar sina egna begränsningsgränser. Eftersom Resource Manager begränsar efter huvud-ID och instans av Resource Manager kan resursleverantören få fler begäranden än standardgränserna i föregående avsnitt.

I det här avsnittet beskrivs begränsningsgränserna för vissa allmänt använda resursleverantörer.

### <a name="storage-throttling"></a>Begränsning av lagring

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Nätverksbegränsning

Microsoft.Network-resursleverantören tillämpar följande begränsningsgränser:

| Åtgärd | Gräns |
| --------- | ----- |
| skriva / ta bort (PUT) | 1000 per 5 minuter |
| läsa (GET) | 10000 per 5 minuter |

### <a name="compute-throttling"></a>Beräkningsbegränsning

Information om begränsningsgränser för beräkningsåtgärder finns i [Felsöka API-begränsningsfel - Beräkna](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md).

Om du vill kontrollera instanser av virtuella datorer i en skaluppsättning för virtuella datorer använder du [åtgärderna För skalningsuppsättningar för virtuella datorer](/rest/api/compute/virtualmachinescalesetvms). Använd till exempel [virtuella datorskalauppsättnings virtuella datorer - Lista](/rest/api/compute/virtualmachinescalesetvms/list) med parametrar för att kontrollera energitillståndet för instanser av virtuella datorer. Det här API:et minskar antalet begäranden.

### <a name="azure-resource-graph-throttling"></a>Begränsning av Azure Resource Graph

[Azure Resource Graph](../../governance/resource-graph/overview.md) begränsar antalet begäranden till dess åtgärder. Stegen i den här artikeln för att fastställa återstående begäranden och hur du ska svara när gränsen nås gäller även för Resursdiagram. Resource Graph anger dock sin egen gräns och återställningshastighet. Mer information finns i [Resursdiagram begränsning rubriker](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

## <a name="error-code"></a>Felkod

När du når gränsen får du HTTP-statuskoden **429 För många begäranden**. Svaret innehåller ett **nytt försök-efter-värde,** som anger hur många sekunder ditt program ska vänta (eller sova) innan nästa begäran skickas. Om du skickar en begäran innan värdet för återförsök har förflutit bearbetas inte din begäran och ett nytt återförsöksvärde returneras.

När du har väntat på angiven tid kan du också stänga och öppna anslutningen till Azure igen. Genom att återställa anslutningen kan du ansluta till en annan instans av Azure Resource Manager.

Om du använder en Azure SDK kan SDK ha en konfiguration för automatiskt återförsök. Mer information finns i [Vägledning för återförsök för Azure-tjänster](/azure/architecture/best-practices/retry-service-specific).

Vissa resursleverantörer returnerar 429 för att rapportera ett tillfälligt problem. Problemet kan vara ett överbelastningstillstånd som inte direkt orsakas av din begäran. Det kan också representera ett tillfälligt fel om målresursens eller den beroende resursens tillstånd. Nätverksresursprovidern returnerar till exempel 429 med felkoden **RetryableErrorDueToAnotherOperation** när målresursen är låst av en annan åtgärd. Om du vill ta reda på om felet kommer från begränsning eller ett tillfälligt tillstånd visar du felinformationen i svaret.

## <a name="remaining-requests"></a>Återstående begäranden

Du kan bestämma antalet återstående begäranden genom att granska svarshuvuden. Läsbegäranden returnerar ett värde i huvudet för antalet återstående läsbegäranden. Skrivbegäranden innehåller ett värde för antalet återstående skrivbegäranden. I följande tabell beskrivs de svarsrubriker som du kan undersöka för dessa värden:

| Svarshuvud | Beskrivning |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-läser |Prenumerationsscopen läses kvar. Det här värdet returneras vid läsåtgärder. |
| x-ms-ratelimit-remaining-subscription-skriver |Prenumerationsscoped skriver kvar. Det här värdet returneras vid skrivåtgärder. |
| x-ms-ratelimit-remaining-tenant-läser |Klientens scoped läser kvar |
| x-ms-ratelimit-remaining-tenant-skriver |Klientens begränsade skrivningar kvar |
| x-ms-ratelimit-remaining-subscription-resource-requests |Begäranden om resurstyp för prenumerationsscope.<br /><br />Det här huvudvärdet returneras endast om en tjänst har åsidosatt standardgränsen. Resource Manager lägger till det här värdet i stället för att prenumerationen läses eller skrivs. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Begäran om insamling av prenumerationsscoped-resurstyp kvarstår.<br /><br />Det här huvudvärdet returneras endast om en tjänst har åsidosatt standardgränsen. Det här värdet anger antalet återstående samlingsbegäranden (listresurser). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Klientomfattad resurstypbegäranden kvar.<br /><br />Det här huvudet läggs bara till för begäranden på klientnivå och endast om en tjänst har åsidosatt standardgränsen. Resource Manager lägger till det här värdet i stället för klienten läser eller skriver. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Klientomfattad resurstyp samlingsbegäranden kvar.<br /><br />Det här huvudet läggs bara till för begäranden på klientnivå och endast om en tjänst har åsidosatt standardgränsen. |

Resursleverantören kan också returnera svarshuvuden med information om återstående begäranden. Information om svarshuvuden som returneras av beräkningsresursprovidern finns i [Svarshuvuden för samtalsfrekvensinformation](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Hämta rubrikvärdena

Att hämta dessa rubrikvärden i koden eller skriptet skiljer sig inte från att hämta något huvudvärde. 

I **C#** hämtar du till exempel rubrikvärdet från ett **HttpWebResponse-objekt** med namnet **svar** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell**hämtar du huvudvärdet från en Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Ett fullständigt PowerShell-exempel finns i [Kontrollera resurshanterarens gränser för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Om du vill se de återstående begärandena om felsökning kan du ange **parametern -Debug** på din PowerShell-cmdlet. **PowerShell**

```powershell
Get-AzResourceGroup -Debug
```

Som returnerar många värden, inklusive följande svarsvärde:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Om du vill hämta skrivgränser använder du en skrivåtgärd: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Som returnerar många värden, inklusive följande värden:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

I **Azure CLI**hämtar du rubrikvärdet med hjälp av det mer utförliga alternativet.

```azurecli
az group list --verbose --debug
```

Som returnerar många värden, inklusive följande värden:

```output
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

Om du vill hämta skrivgränser använder du en skrivåtgärd: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Som returnerar många värden, inklusive följande värden:

```output
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

* Ett fullständigt PowerShell-exempel finns i [Kontrollera resurshanterarens gränser för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Mer information om begränsningar och kvoter finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Mer information om hur du hanterar asynkrona REST-begäranden finns i [Spåra asynkrona Azure-åtgärder](async-operations.md).
