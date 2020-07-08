---
title: Felsöka begränsnings fel i Azure | Microsoft Docs
description: Begränsnings fel, återförsök och backoff i Azure Compute.
services: virtual-machines
documentationcenter: ''
author: changov
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: changov
ms.reviewer: vashan, rajraj
ms.openlocfilehash: f5fbd80fc9a8e519cf8f49ab16d7e747c6a8171b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "76045355"
---
# <a name="troubleshooting-api-throttling-errors"></a>Felsökning av fel med API-begränsningar 

Azure Compute-begäranden kan begränsas till en prenumeration och per region för att hjälpa till med den övergripande prestandan för tjänsten. Vi ser till att alla anrop till Azure Compute Resource Provider (CRP), som hanterar resurser under Microsoft. Compute-namnrymden, inte överskrider den högsta tillåtna frekvensen för API-begäranden. I det här dokumentet beskrivs API-begränsning, information om hur du felsöker problem med begränsning och metod tips för att undvika begränsning.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Begränsning av Azure Resource Manager vs-resurs leverantörer  

Som första dörren till Azure är Azure Resource Manager autentisering och första sortering och begränsning av alla inkommande API-begäranden. Azure Resource Manager samtals frekvens gränser och relaterade diagnostiska svars-HTTP-huvuden beskrivs [här](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling).
 
När en Azure API-klient får ett begränsnings fel är HTTP-statusen 429 för många begär Anden. Om du vill ta reda på om begäran begränsas av Azure Resource Manager eller en underliggande resurs leverantör som CRP, kontrollerar du `x-ms-ratelimit-remaining-subscription-reads` för Get-begäranden och `x-ms-ratelimit-remaining-subscription-writes` -svarshuvuden för förfrågningar som inte hämtats. Om det återstående antalet anrop närmar sig 0, har prenumerationens allmänna anrops gräns som definieras av Azure Resource Manager nåtts. Aktiviteter av alla prenumerations klienter räknas tillsammans. Annars kommer begränsningen från mål resurs leverantören (den som anges av `/providers/<RP>` segmentet i URL: en för begäran). 

## <a name="call-rate-informational-response-headers"></a>Svars rubriker för information om samtals frekvens 

| Sidhuvud                            | Värde format                           | Exempel                               | Beskrivning                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-MS-ratelimit-återstående-resurs |```<source RP>/<policy or bucket>;<count>```| Microsoft. Compute/HighCostGet3Min; 159 | Återstående API-anrop för begränsnings principen som täcker resurs Bucket eller åtgärds gruppen, inklusive målet för denna begäran                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Antalet anrops antal "debiteras" för denna HTTP-begäran mot gällande princip gräns. Detta är oftast 1. Batch-begäranden, till exempel för skalning av en virtuell dators skalnings uppsättning, kan debitera flera antal. |


Observera att en API-begäran kan utsättas för flera begränsnings principer. Det kommer att finnas en separat `x-ms-ratelimit-remaining-resource` rubrik för varje princip. 

Här är ett exempel svar på borttagning av begäran om skalnings uppsättning för virtuell dator.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Fel information om begränsning

HTTP-statusen 429 används ofta för att avvisa en begäran eftersom gränsen för anrops frekvensen uppnås. Ett typiskt begränsnings fel svar från Compute Resource providern ser ut som exemplet nedan (endast relevanta rubriker visas):

```
HTTP/1.1 429 Too Many Requests
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet3Min;46
x-ms-ratelimit-remaining-resource: Microsoft.Compute/HighCostGet30Min;0
Retry-After: 1200
Content-Type: application/json; charset=utf-8
{
  "code": "OperationNotAllowed",
  "message": "The server rejected the request because too many requests have been received for this subscription.",
  "details": [
    {
      "code": "TooManyRequests",
      "target": "HighCostGet30Min",
      "message": "{\"operationGroup\":\"HighCostGet30Min\",\"startTime\":\"2018-06-29T19:54:21.0914017+00:00\",\"endTime\":\"2018-06-29T20:14:21.0914017+00:00\",\"allowedRequestCount\":800,\"measuredRequestCount\":1238}"
    }
  ]
}

```

Principen med det återstående anrop svärdet 0 är den som beror på att begränsnings felet returneras. I det här fallet `HighCostGet30Min` . Det övergripande formatet för svars texten är allmänt Azure Resource Manager API-felmeddelande (följer OData). Huvud fel koden, `OperationNotAllowed` , är den enda Compute Resource providern som använder för att rapportera begränsnings fel (bland andra typer av klient fel). `message`Egenskapen för de inre fel meddelandena innehåller en serialiserad JSON-struktur med information om begränsnings felet.

Som illustreras ovan innehåller varje begränsnings fel `Retry-After` sidhuvudet, vilket ger det minsta antal sekunder som klienten ska vänta innan den försöker utföra begäran igen. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-anrops frekvens och begränsning av fel analys
En för hands version av en fel söknings funktion är tillgänglig för Compute Resource providerns API. Dessa PowerShell-cmdletar ger statistik om frekvensen för API-begäranden per tidsintervall per åtgärd och begränsnings överträdelser per åtgärds grupp (princip):
-   [Exportera – AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Exportera – AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

API-anropets statistik kan ge fantastiska insikter om beteendet för en prenumerations klient (er) och möjliggör enkel identifiering av anrops mönster som orsakar begränsning.

En begränsning av analys tiden är att det inte räknas begär Anden för disk-och ögonblicks bilds resurs typer (stöd för hanterade diskar). Eftersom det samlar in data från CRP ' s telemetri, kan det inte heller hjälpa till att identifiera begränsnings fel från ARM. Men de kan identifieras enkelt baserat på de olika svars rubrikerna på ARM, enligt beskrivningen ovan.

PowerShell-cmdlets använder ett REST-tjänst-API, som enkelt kan anropas direkt av klienter (trots att det inte finns något formellt stöd ännu). Om du vill se HTTP-förfrågningens format kör du cmdletarna med-debug-växeln eller snooping vid körning med Fiddler.


## <a name="best-practices"></a>Bästa praxis 

- Försök inte igen Azure Service API-fel utan villkor och/eller direkt. En vanlig förekomst är att klient koden kommer till en snabb återförsöks slinga när ett fel påträffas. Nya försök kommer att ta slut på den tillåtna anrops gränsen för mål åtgärdens grupp och påverka andra klienter i prenumerationen. 
- I API Automation-scenarier med hög volym bör du överväga att implementera proaktiv begränsning på klient sidan när antalet tillgängliga anrop för en mål åtgärds grupp sjunker under en viss låg tröskel. 
- När du spårar asynkrona åtgärder bör du följa tipsen för återförsök efter huvud. 
- Om klient koden behöver information om en viss virtuell dator, fråga den virtuella datorn direkt i stället för att visa alla virtuella datorer i resurs gruppen som innehåller resurs gruppen eller hela prenumerationen och sedan välja den virtuella datorn som behövs på klient sidan. 
- Om klient koden behöver virtuella datorer, diskar och ögonblicks bilder från en annan Azure-plats, använder du platsbaserade formulär för frågan i stället för att fråga alla virtuella prenumerationer och sedan filtrera efter plats på klient sidan: `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` fråga till Compute Resource Provider regionala slut punkter. 
-   När du skapar eller uppdaterar API-resurser i synnerhet virtuella datorer och skalnings uppsättningar för virtuella datorer är det mycket mer effektivt att spåra den returnerade asynkrona åtgärden till slut för ande än att avsöka på resurs-URL: en (baserat på `provisioningState` ).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du gör nya försök för andra tjänster i Azure finns i [vägledning för att försöka igen för vissa tjänster](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
