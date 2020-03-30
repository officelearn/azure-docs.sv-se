---
title: Felsöka begränsningsfel i Azure | Microsoft-dokument
description: Begränsningsfel, återförsök och backoff i Azure Compute.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045355"
---
# <a name="troubleshooting-api-throttling-errors"></a>Felsökning av fel med API-begränsningar 

Azure Compute-begäranden kan begränsas vid en prenumeration och per region för att hjälpa till med tjänstens övergripande prestanda. Vi ser till att alla anrop till Azure Compute Resource Provider (CRP), som hanterar resurser under Microsoft.Compute-namnområdet inte överskrider den högsta tillåtna API-begäranden. I det här dokumentet beskrivs API-begränsning, information om hur du felsöker begränsningsproblem och metodtips för att undvika att begränsas.  

## <a name="throttling-by-azure-resource-manager-vs-resource-providers"></a>Begränsning av Azure Resource Manager vs Resursleverantörer  

Som ytterdörren till Azure gör Azure Resource Manager autentisering och första ordningens validering och begränsning av alla inkommande API-begäranden. Azure Resource Manager-samtalshastighetsbegränsningar och relaterade HTTP-svarshuvuden för diagnostiksvar beskrivs [här](https://docs.microsoft.com/azure/azure-resource-manager/management/request-limits-and-throttling).
 
När en Azure API-klient får ett begränsningsfel är HTTP-status 429 för många begäranden. För att förstå om begäran begränsning görs av Azure Resource Manager eller en `x-ms-ratelimit-remaining-subscription-reads` underliggande resursleverantör som CRP, inspektera för GET-begäranden och `x-ms-ratelimit-remaining-subscription-writes` svarshuvuden för icke-GET-begäranden. Om det återstående antalet samtal närmar sig 0 har prenumerationens allmänna samtalsgräns som definierats av Azure Resource Manager uppnåtts. Aktiviteter av alla prenumerationskunder räknas tillsammans. Annars kommer begränsningen från målresursprovidern (den som `/providers/<RP>` tas upp av segmentet i begäran-URL:en). 

## <a name="call-rate-informational-response-headers"></a>Svarsrubriker för samtalsfrekvens 

| Huvud                            | Värdeformat                           | Exempel                               | Beskrivning                                                                                                                                                                                               |
|-----------------------------------|----------------------------------------|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| x-ms-ratelimit-återstående resurs |```<source RP>/<policy or bucket>;<count>```| Microsoft.Compute/HighCostGet3Min;159 | Återstående ANTAL API-anrop för begränsningsprincipen som täcker resurssegmentet eller åtgärdsgruppen, inklusive målet för den här begäran                                                                   |
| x-ms-request-charge               | ```<count>```                             | 1                                     | Antalet samtalsantal som "debiteras" för den här HTTP-begäran mot den tillämpliga principens gräns. Detta är oftast 1. Batchbegäranden, till exempel för skalning av en skalningsuppsättning för virtuella datorer, kan debitera flera antal. |


Observera att en API-begäran kan utsättas för flera begränsningsprinciper. Det kommer att `x-ms-ratelimit-remaining-resource` finnas en separat rubrik för varje princip. 

Här är ett exempel svar på att ta bort virtuell dator skala uppsättning begäran.

```
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet3Min;107 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/DeleteVMScaleSet30Min;587 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VMScaleSetBatchedVMRequests5Min;3704 
x-ms-ratelimit-remaining-resource: Microsoft.Compute/VmssQueuedVMOperations;4720 
```

## <a name="throttling-error-details"></a>Felinformation för begränsning

429 HTTP-status används ofta för att avvisa en begäran eftersom en samtalshastighetsgräns har uppnåtts. Ett vanligt begränsningsfelsvar från Compute Resource Provider ser ut som exemplet nedan (endast relevanta rubriker visas):

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

Principen med återstående antal samtal på 0 är den som begränsningsfelet returneras till. I detta fall `HighCostGet30Min`som är . Det övergripande formatet för svarstexten är det allmänna API-felformatet för Azure Resource Manager (överensstämmer med OData). Huvudfelkoden `OperationNotAllowed`är den beräkningsresursprovider som används för att rapportera begränsningsfel (bland andra typer av klientfel). Egenskapen `message` för de inre felen innehåller en serialiserad JSON-struktur med information om begränsningsfel.

Som illustreras ovan innehåller varje `Retry-After` begränsningsfel huvudet, vilket ger det minsta antalet sekunder som klienten ska vänta innan begäran försökers igen. 

## <a name="api-call-rate-and-throttling-error-analyzer"></a>API-anropshastighet och begränsningsfelanalysator
En förhandsgranskningsversion av en felsökningsfunktion är tillgänglig för Beräkningsresursleverantörens API. Dessa PowerShell-cmdlets ger statistik om API-begäranden per tidsintervall per åtgärd och begränsningsöverträdelser per åtgärdsgrupp (princip):
-   [Exportera-AzLogAnalyticRequestRateByInterval](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticrequestratebyinterval)
-   [Export-AzLogAnalyticThrottledRequest](https://docs.microsoft.com/powershell/module/az.compute/export-azloganalyticthrottledrequest)

API-anropsstatistiken kan ge stor insikt i beteendet hos en prenumerationsklient(er) och möjliggör enkel identifiering av samtalsmönster som orsakar begränsning.

En begränsning av analysatorn för närvarande är att den inte räknar begäranden för disk- och ögonblicksbildresurstyper (till stöd för hanterade diskar). Eftersom den samlar in data från CRP:s telemetri kan den inte heller hjälpa till att identifiera begränsningsfel från ARM. Men de kan identifieras lätt baserat på de distinkta ARM svar rubriker, som diskuterats tidigare.

PowerShell-cmdlets använder en REST-tjänst API, som lätt kan anropas direkt av klienter (men utan formellt stöd ännu). Om du vill se HTTP-begärandeformatet kör du cmdlets med -Debug-växeln eller snoka på deras körning med Fiddler.


## <a name="best-practices"></a>Bästa praxis 

- Försök inte igen Azure-tjänst API-fel villkorslöst och/eller omedelbart. En vanlig förekomst är att klientkoden hamnar i en snabb nyförsöksloop när du stöter på ett fel som inte kan försöka igen. Återförsök kommer så småningom att uttömma den tillåtna samtalsgränsen för målåtgärdens grupp och påverka andra kunder i prenumerationen. 
- I API-automatiseringsärenden med hög volym bör du överväga att implementera proaktiv självbegränsning på klientsidan när det tillgängliga antalet samtal för en målgrupp för målåtgärder sjunker under ett lågt tröskelvärde. 
- När du spårar asynkroniseringsåtgärder respekterar du sidledstipsen För återförsök-efter. 
- Om klientkoden behöver information om en viss virtuell dator, fråga den virtuella datorn direkt i stället för att lista alla virtuella datorer i den innehållande resursgruppen eller hela prenumerationen och sedan välja den nödvändiga virtuella datorn på klientsidan. 
- Om klientkod behöver virtuella datorer, diskar och ögonblicksbilder från en viss Azure-plats använder du platsbaserad form av frågan i `GET /subscriptions/<subId>/providers/Microsoft.Compute/locations/<location>/virtualMachines?api-version=2017-03-30` stället för att fråga alla virtuella prenumerations-datorer och sedan filtrera efter plats på klientsidan: fråga till Regionala slutpunkter för Beräkningsresursprovider. 
-   När api-resurser skapas eller uppdateras i synnerhet, virtuella datorer och skaluppsättningar för virtuella datorer är det mycket effektivare `provisioningState`att spåra den returnerade asynkronåtgärden till slutförande än avsökning på själva resurs-URL:en (baserat på ).

## <a name="next-steps"></a>Nästa steg

Mer information om vägledning för återförsök för andra tjänster i Azure finns i [Vägledning för återförsök för specifika tjänster](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)
