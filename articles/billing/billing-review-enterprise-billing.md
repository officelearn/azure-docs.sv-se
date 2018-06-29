---
title: Granska Azure enterprise-registrering fakturering data med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska faktureringsinformation för enterprise-registrering.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064114"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Granska enterprise registrering fakturering med hjälp av REST API: er

Azure Reporting API: er hjälper dig att granska och hantera dina Azure kostnader.

Här kan du lära dig att hämta aktuella faktura som är associerad med ett konto enterprise-registrering.

Att hämta den aktuella fakturan:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Skapa begäran  

Den `{enrollmentID}` parametern krävs och måste innehålla registrering-ID för Enterprise konto (EA).

Följande huvuden krävs: 

|Huvudet i begäran|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange att en giltig `Bearer` [API-nyckeln](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Det här exemplet illustrerar ett synkrona anrop som returnerar information för den aktuella faktureringsperioden. Av prestandaskäl returnera synkrona anrop information för den senaste månaden.  Du kan också anropa den [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) att returnera data för 36 månader.


## <a name="response"></a>Svar  

Statuskoden 200 returneras (OK) för ett lyckat svar, som innehåller en lista med detaljerade kostnader för ditt konto.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Varje objekt i **data** representerar en avgift:

|Egenskapen svar|Beskrivning|
|----------------|----------|
|**Kostnad** | Hur mycket använts i en valuta som är lämpliga för platsen för datacenter. |
|**subscriptionGuid** | Globalt unikt ID för prenumerationen. | 
|**departmentId** | ID för avdelning, om sådana finns. |
|**Datum** | Tillägget har debiteras datum. |
|**tagg** | JSON-sträng som innehåller taggar som är associerade med prenumerationen. |
|**resourceGroup**|Namnet på resursgruppen som innehåller det objekt som kostnaden. |
|**nextLink**| När värdet anger en URL för nästa ”sida” i information. Tom när sidan är den sista. |  
||
  
Avdelning-ID: N, resursgrupper, taggar och relaterade fält har definierats av EA-administratör.  

Det här exemplet är förkortas; Se [få information om användning](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) för en fullständig beskrivning av varje svarsfält. 

Andra statuskoder indikerar fel. I dessa fall response-objektet som förklarar varför begäran misslyckades.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Nästa steg 
- Granska [Enterprise översikt över rapportering](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersöka [Enterprise fakturering REST-API](https://docs.microsoft.com/rest/api/billing/)   
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
