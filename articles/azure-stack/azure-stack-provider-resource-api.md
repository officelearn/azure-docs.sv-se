---
title: Providerresursanvändning | Microsoft Docs
description: Referens för Resursanvändning API, som hämtar information för användningen av Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/24/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: aedaa729ec51d7b60b2c242239935f7b3e41794f
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918195"
---
# <a name="provider-resource-usage-api"></a>API för providerresursanvändning
Termen *provider* gäller tjänstadministratören och alla delegerade providrar. Azure Stack-operatörer och delegerade providrar kan använda provideranvändning för att visa användningen av sina direkta klienter. Till exempel i diagrammet visas P0 kan anropa providern API för att få information om användningen på P1 och P2's direkt användning och P1 kan anropa användningsinformation för P3 och P4.

![Konceptuell modell för hierarkin provider](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referens för API-anrop
### <a name="request"></a>Förfrågan
Begäran hämtar information om förbrukning för de begärda prenumerationerna och för den begärda tidsramen. Det finns inga begärandetexten.

Den här användningen API är en leverantör API, så att anroparen måste tilldelas en ägare, deltagare eller läsare roll i leverantörens prenumeration.

| **Metoden** | **Begärande-URI** |
| --- | --- |
| HÄMTA |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {token-value} |

### <a name="arguments"></a>Argument
| **Argumentet** | **Beskrivning** |
| --- | --- |
| *armendpoint* |Azure Resource Manager-slutpunkten för Azure Stack-miljön. Azure Stack-konventionen är att namnet på Azure Resource Manager-slutpunkten är i formatet `https://adminmanagement.{domain-name}`. Till exempel för development kit om domännamnet är *local.azurestack.external*, Resource Manager-slutpunkten är `https://adminmanagement.local.azurestack.external`. |
| *subId* |Prenumerations-ID för den användare som gör anropet. |
| *reportedStartTime* |Starttid för frågan. Värdet för *DateTime* ska vara i Coordinated Universal Time (UTC) och i början av timme, till exempel 13:00. För dagliga aggregering, ange ett värde till midnatt i UTC. Formatet är *undantagna* ISO 8601. Till exempel *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, där kolumnen är undantaget till *% 3a* och plustecknet hoppas att *% 2b* så att den är URI: N eget. |
| *reportedEndTime* |Sluttid för frågan. De begränsningar som gäller för *reportedStartTime* gäller även för det här argumentet. Värdet för *reportedEndTime* får inte vara i framtiden eller det aktuella datumet. Om det är är resultatet inställd på ”behandlingen har inte slutförts”. |
| *aggregationGranularity* |Valfri parameter som har två diskreta möjliga värden: varje dag och per timme. Eftersom värdena föreslå en returnerar data i daglig kornighet och den andra är upplösningen per timme. Dagliga alternativet är standardinställningen. |
| *subscriberId* |Prenumerations-ID. Prenumerations-ID för en direkt klient av providern krävs för att hämta filtrerade data. Om ingen prenumerations-ID-parameter har angetts i anropet returnerar användningsdata för alla leverantörens direkt klienter. |
| *API-versionen* |Version av det protokoll som används för att göra denna begäran. Det här värdet anges till *2015-06-01-preview*. |
| *continuationToken* |Token hämtas från det senaste anropet till användnings-API-providern. Den här token krävs när ett svar är större än 1 000 rader och den fungerar som ett bokmärke för information om förloppet. Om token inte är tillgänglig, data hämtas från början på dagen eller timme, baserat på precisionen skickas in. |

### <a name="response"></a>Svar
Hämta /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = varje dag & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Svarsinformation
| **Argumentet** | **Beskrivning** |
| --- | --- |
| *ID* |Unikt ID för användning aggregering. |
| *Namn* |Namnet på samlingen användning. |
| *typ* |Resursdefinitionen. |
| *Prenumerations-ID* |Prenumerations-ID för Azure Stack-användare. |
| *usageStartTime* |UTC starttid för bucketen användning som den här aggregeringen användningen tillhör.|
| *usageEndTime* |UTC-sluttid för bucketen användning som den här aggregeringen användningen tillhör. |
| *instanceData* |Nyckel / värde-par med instansinformation (i ett nytt format):<br> *resourceUri*: fullständigt kvalificerade resurs-ID, vilket inkluderar resursgrupper och instansnamnet. <br> *plats*: Region där den här tjänsten kördes. <br> *taggar*: resurstaggar som anges av användaren. <br> *additionalInfo*: Mer information om den resurs som förbrukades, till exempel OS-version eller avbildning typen. |
| *Kvantitet* |Mängden resursförbrukning som inträffat under det här tidsintervallet. |
| *MeterId* |Unikt ID för den resurs som förbrukades (kallas även *ResourceID*). |


## <a name="retrieve-usage-information"></a>Hämta information om användning

### <a name="powershell"></a>PowerShell

För att generera användningsdata, bör du ha resurser som körs och som aktivt använder systemet, till exempel, en aktiv virtuell dator eller ett lagringskonto som innehåller vissa data osv. Om du inte vet om du har några resurser som körs i Azure Stack Marketplace, distribuera en virtuell dator (VM) och kontrollera den virtuella datorn körs övervakning bladet för att se till att den. Använd följande PowerShell-cmdletar för att visa användningsdata:

1. [Installera PowerShell för Azure Stack.](azure-stack-powershell-install.md)
2. [Konfigurera Azure Stack-användare](user/azure-stack-powershell-configure-user.md) eller [Azure Stack-operatör](azure-stack-powershell-configure-admin.md) PowerShell-miljö 
3. Använd för att hämta användningsdata i [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell-cmdlet:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>REST-API

Du kan samla in användningsinformation för borttagna prenumerationer genom att anropa tjänsten Microsoft.Commerce.Admin. 

**Tas bort för aktiva användare för att returnera alla klientanvändning för:**

| **Metoden** | **Begärande-URI** |
| --- | --- |
| HÄMTA | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-preview |

**Att returnera användningen av borttagna eller aktiv klient:**

| **Metoden** | **Begärande-URI** |
| --- | --- |
| HÄMTA |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ prenumerations-id} & api-version = 2015-06-01-preview |


## <a name="next-steps"></a>Nästa steg
[Klient-Resursanvändning API-referens](azure-stack-tenant-resource-usage-api.md)

[Användningsrelaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)
