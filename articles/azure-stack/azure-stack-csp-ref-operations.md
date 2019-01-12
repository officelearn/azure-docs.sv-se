---
title: Registrera klienter för användningsspårning i Azure Stack | Microsoft Docs
description: Information om åtgärder som används för att hantera klient registreringar och hur klientanvändning spåras i Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/08/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: af3858ae283213de1591cf8f3c4e2acb307ce182
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246136"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Hantera klienten registrering i Azure Stack

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln innehåller information om registreringsåtgärder. Du kan använda de här åtgärderna till:
- Hantera registreringar för klient
- Hantera spårning för användning av klient

Du hittar information om hur du lägger till, lista, eller ta bort klient mappningar. Du kan använda PowerShell eller Billing API-slutpunkter för att hantera din användning spårning. Du hittar information om hur du lägger till, lista, eller ta bort klient mappningar. Du kan använda PowerShell eller Billing API-slutpunkter för att hantera din användning spårning.

## <a name="add-tenant-to-registration"></a>Lägg till klient till registreringen

Du använder åtgärden när du vill lägga till en ny klient i din registrering. Klientanvändning rapporteras under en Azure-prenumeration i samband med sina Azure Active Directory (Azure AD)-klient.

Du kan också använda åtgärden om du vill ändra den prenumeration som är associerad med en klient. Anropa PUT/New-AzureRMResource om du vill skriva över tidigare mappningen.

Du kan associera en enda Azure-prenumeration med en klient. Om du försöker lägga till en andra prenumerationen till en befintlig klient, är den första prenumerationen skrivas över.

### <a name="use-api-profiles"></a>Använd API-profiler

Registrering-cmdletar kräver att du anger en API-profil när du kör PowerShell. API-profiler representerar en uppsättning Azure resursprovidrar och deras API-versioner. De hjälper dig att använda rätt version av API: et när du interagerar med flera Azure-moln. Exempelvis kan arbeta du med flera moln när du arbetar med globala Azure och Azure Stack. Profiler ange ett namn som matchar deras lanseringsdatum. Du måste använda den **2017-09-03** profil.

Läs mer om Azure Stack och API-profiler, [hantera API-versionsprofiler i Azure Stack](user/azure-stack-version-profiles.md). Anvisningar för att konfigurera och komma igång med API-profilen med PowerShell finns i [Använd API-versionsprofiler för PowerShell i Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parametrar

| Parameter                  | Beskrivning |
|---                         | --- |
| registrationSubscriptionID | Azure-prenumerationen som har använts för den första registreringen. |
| customerSubscriptionID     | Azure-prenumerationen (inte Azure Stack) som hör till kunden som ska registreras. Måste skapas i Cloud Service Provider (CSP) erbjudandet via Partnercenter. Om en kund har fler än en klient, skapat en prenumeration att logga in på Azure Stack-klient. |
| resourceGroup              | Resursgruppen i Azure som är lagrade i din registrering. |
| registrationName           | Namnet på registreringen av din Azure Stack. Det är ett objekt som lagras i Azure. Namnet är vanligtvis i formuläret azurestack-CloudID, där CloudID är moln-ID för Azure Stack-distributionen. |

> [!Note]  
> Klienter måste vara registrerad med varje Azure Stack som de använder. Om en klient använder mer än en Azure Stack, måste du uppdatera de inledande registreringarna för varje distribution med klientprenumeration.

### <a name="powershell"></a>PowerShell

Använd cmdleten New-AzureRmResource för att uppdatera resursen för registrering. Här är ett exempel på hur du lägger till en klient:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API-anrop

**Åtgärden**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Svaret**: 201 Skapad  
**Svarstexten**: Tom  

## <a name="list-all-registered-tenants"></a>Lista över alla registrerade klienter

Hämta en lista över alla klienter som har lagts till en registrering.

 > [!Note]  
 > Om inga klienter har registrerats kan du inte tar emot ett svar.

### <a name="parameters"></a>Parametrar

| Parameter                  | Beskrivning          |
|---                         | ---                  |
| registrationSubscriptionId | Azure-prenumerationen som har använts för den första registreringen.   |
| resourceGroup              | Resursgruppen i Azure som är lagrade i din registrering.    |
| registrationName           | Namnet på registreringen av din Azure Stack. Det är ett objekt som lagras i Azure. Namnet på är vanligtvis i form av **azurestack**-***CloudID***, där ***CloudID*** är moln-ID för Azure Stack-distributioner.   |

### <a name="powershell"></a>PowerShell

Använd cmdleten Get-AzureRmResource att lista alla registrerade klienter. Logga in på Azure (`Add-AzureRmAccount`) med hjälp av det konto du använde för den första registreringen. Här är ett exempel på hur du lägger till en klient:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-anrop

Du kan hämta en lista över alla klient-mappningar med hjälp av GET-åtgärd

**Åtgärden**: HÄMTA  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Svaret**: 200  
**Svarstexten**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Ta bort en klientmappning

Du kan ta bort en klient som har lagts till en registrering. Om klienten fortfarande använder resurser på Azure Stack, debiteras deras användning för prenumerationen som används i den första registreringen i Azure Stack.

### <a name="parameters"></a>Parametrar

| Parameter                  | Beskrivning          |
|---                         | ---                  |
| registrationSubscriptionId | Prenumerations-ID för registreringen.   |
| resourceGroup              | Resursgrupp för registreringen.   |
| registrationName           | Namnet på registreringen.  |
| customerSubscriptionId     | Kundens prenumerations-ID.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-anrop

Du kan ta bort klient mappningarna med åtgärden ta bort.

**Åtgärden**: DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Svaret**: 204 inget innehåll  
**Svarstexten**: Tom

## <a name="next-steps"></a>Nästa steg

 - Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](azure-stack-billing-and-chargeback.md).
