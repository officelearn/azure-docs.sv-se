---
title: Granska fakturerings data för Azure Enterprise-registrering med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST-API: er för att granska fakturerings information för företags registrering.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443154"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Granska fakturering av företags registrering med hjälp av REST API: er

Azure repor ting-API: er hjälper dig att granska och hantera dina Azure-kostnader.

I den här artikeln får du lära dig att hämta fakturerings information som är kopplad till fakturerings konton, avdelnings konton eller EA-registrerade (Enterprise Agreement) med hjälp av Azure REST-API: er. 

## <a name="individual-account-billing"></a>Fakturering av enskilda konton

För att få användnings information för konton på en avdelning:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` Parametern är obligatorisk och ska innehålla ID: t för kontot.

Följande rubriker krävs: 

|Begär ande huvud|Beskrivning|  
|--------------------|-----------------|  
|*Innehålls typ:*|Obligatoriskt. Ange till `application/json`.|  
|*Authorization:*|Obligatoriskt. Ange en giltig `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella fakturerings perioden. Av prestanda skäl returnerar synkrona anrop information under den senaste månaden.  Du kan också anropa [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data i 36 månader.


## <a name="response"></a>Svar  

Status koden 200 (OK) returneras för ett lyckat svar som innehåller en lista med detaljerade kostnader för kontot.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortat; Se [Hämta användnings information för ett fakturerings konto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) för en fullständig beskrivning av varje svars fält och fel hantering.

## <a name="department-billing"></a>Avdelnings fakturering 

Hämta användnings information som sammanställs för alla konton på en avdelning. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` Parametern är obligatorisk och ska innehålla ID: t för avdelningen i registrerings kontot.

Följande rubriker krävs: 

|Begär ande huvud|Beskrivning|  
|--------------------|-----------------|  
|*Innehålls typ:*|Obligatoriskt. Ange till `application/json`.|  
|*Authorization:*|Obligatoriskt. Ange en giltig `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella fakturerings perioden. Av prestanda skäl returnerar synkrona anrop information under den senaste månaden.  Du kan också anropa [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data i 36 månader.

### <a name="response"></a>Svar  

Status koden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerad användnings information och kostnader för en viss fakturerings period och ett faktura-ID för avdelningen.


I följande exempel visas utdata från REST API för avdelning `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortat; Se [Hämta användnings information för en avdelning](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) för en fullständig beskrivning av varje svars fält och fel hantering.

## <a name="enrollment-account-billing"></a>Fakturering av registrerings konto

Hämta användnings information som sammanställs för registrerings kontot.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` Parametern är obligatorisk och ska innehålla ID: t för registrerings kontot.

Följande rubriker krävs: 

|Begär ande huvud|Beskrivning|  
|--------------------|-----------------|  
|*Innehålls typ:*|Obligatoriskt. Ange till `application/json`.|  
|*Authorization:*|Obligatoriskt. Ange en giltig `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

I det här exemplet visas ett synkront anrop som returnerar information om den aktuella fakturerings perioden. Av prestanda skäl returnerar synkrona anrop information under den senaste månaden.  Du kan också anropa [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) för att returnera data i 36 månader.

### <a name="response"></a>Svar  

Status koden 200 (OK) returneras för ett lyckat svar, som innehåller en lista med detaljerad användnings information och kostnader för en viss fakturerings period och ett faktura-ID för avdelningen.

I följande exempel visas resultatet av REST API för företags registrering `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Det här exemplet är förkortat; Se [Hämta användnings information för ett registrerings konto](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) för en fullständig beskrivning av varje svars fält och fel hantering.

## <a name="next-steps"></a>Nästa steg 
- Granska [Översikt över företags rapportering](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersök [företagets fakturerings REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
