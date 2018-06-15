---
title: Registrera klienter för användning av spårning i Azure-stacken | Microsoft Docs
description: Information om åtgärder som används för att hantera klient registreringar och hur klientanvändning spåras i Azure-stacken.
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159119"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Hantera innehavare registrering i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Den här artikeln innehåller information om åtgärder som du kan använda för att hantera klient-registreringar och hur klientanvändning spåras. Du hittar information om hur du lägger till, lista, eller ta bort innehavaren mappningar. Du kan använda PowerShell eller fakturering API-slutpunkter för att hantera din användning spårning.

## <a name="add-tenant-to-registration"></a>Lägg till klient för registrering

Du använder den här åtgärden när du vill lägga till en ny klient för din registrering, så att deras användning rapporteras under en Azure-prenumeration i samband med sina Azure Active Directory (Azure AD)-klient.

Du kan också använda den här åtgärden om du vill ändra prenumerationen som är kopplade till en klient, kan du anropa ny/PUT-AzureRMResource igen. Gamla mappningen skrivs över.

Observera att endast en Azure-prenumeration kan vara kopplad till en klient. Om du försöker lägga till en andra prenumeration i en befintlig klient är första prenumerationen felaktigt skrivet. 


| Parameter                  | Beskrivning |
|---                         | --- |
| registrationSubscriptionID | Azure-prenumerationen som användes för registreringen. |
| customerSubscriptionID     | Azure-prenumerationen (inte Azure-Stack) som hör till kunden ska registreras. Måste skapas i erbjudandet molntjänst-providers (CSP). I praktiken innebär detta via Partnercenter. Om en kund har mer än en klient, måste den här prenumerationen skapas i innehavaren som ska användas för att logga in på Azure-stacken. |
| resourceGroup              | Resursgrupp i Azure som är lagrade i din registrering. |
| registrationName           | Namnet på registrering av Azure-stacken. Det är ett objekt som lagras i Azure. Namnet är vanligtvis i formuläret azurestack-CloudID, där CloudID är moln-ID för din Azure Stack-distribution. |

> [!Note]  
> Klienter måste vara registrerad med varje Azure-stacken som de använder. Om en klient använder mer än en Azure-stacken, måste du uppdatera de inledande registreringarna för varje distribution med klientprenumeration.

### <a name="powershell"></a>PowerShell

Använd cmdleten New-AzureRmResource för att uppdatera resursen för registrering. Logga in i Azure (`Add-AzureRmAccount`) med det konto som du använde för den första registreringen. Här är ett exempel på hur du lägger till en klient:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API-anrop

**Åtgärden**: PLACERA  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Svaret**: 201 skapas  
**Svarstexten**: tom  

## <a name="list-all-registered-tenants"></a>Visa en lista över alla registrerade klienter

Hämta en lista över alla klienter som har lagts till en registrering.

 > [!Note]  
 > Ett svar emot inte om inga klienter har registrerats.

### <a name="parameters"></a>Parametrar

| Parameter                  | Beskrivning          |
|---                         | ---                  |
| registrationSubscriptionId | Azure-prenumerationen som användes för registreringen.   |
| resourceGroup              | Resursgrupp i Azure som är lagrade i din registrering.    |
| registrationName           | Namnet på registrering av Azure-stacken. Det är ett objekt som lagras i Azure. Namnet på är vanligtvis i form av **azurestack**-***CloudID***, där ***CloudID*** är moln-ID för din Azure Stack-distribution.   |

### <a name="powershell"></a>PowerShell

Använd cmdleten Get-AzureRmResovurce för att visa alla registrerade klienter. Logga in i Azure (`Add-AzureRmAccount`) med det konto som du använde för den första registreringen. Här är ett exempel på hur du lägger till en klient:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-anrop

Du kan hämta en lista över alla klient-mappningar med hjälp av GET-åtgärd

**Åtgärden**: hämta  
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

## <a name="remove-a-tenant-mapping"></a>Ta bort en klient mappning

Du kan ta bort en klient som har lagts till i en registrering. Om att klienten fortfarande använder resurser i Azure-stacken, debiteras sin användning till registreringen Azure Stack-prenumerationen.

### <a name="parameters"></a>Parametrar

| Parameter                  | Beskrivning          |
|---                         | ---                  |
| registrationSubscriptionId | Prenumerations-ID för registrering.   |
| resourceGroup              | Resursgruppen för registrering.   |
| registrationName           | Namnet på registreringen.  |
| customerSubscriptionId     | Kunden prenumerations-ID.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-anrop

Du kan ta bort innehavaren mappningarna med DELETE-åtgärden.

**Åtgärden**: ta bort  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Svaret**: 204 inget innehåll  
**Svarstexten**: tom

## <a name="next-steps"></a>Nästa steg

 - Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](/azure-stack-billing-and-chargeback.md).
