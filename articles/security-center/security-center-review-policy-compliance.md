---
title: Granska efterlevnaden av hanteringsprinciper för Security Center med Azure REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska aktuella kompatibilitet med Security Center principer.'
services: security-center
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: alleonar
ms.openlocfilehash: 6c6764eec59633f0bdd0fa396c1581117a0c1e1d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2018
---
# <a name="review-security-center-policy-compliance-using-rest-apis"></a>Granska Security Center policy kompatibilitet med hjälp av REST API: er

Security Center verifierar regelbundet dina Azure-resurser mot dina definierade säkerhetsprinciper. Security Center innehåller också en REST-API som gör att du kan kontrollera kompatibiliteten från ditt eget program. Du kan skicka frågor till tjänsten direkt eller importera JSON-resultaten till andra program. 

Här kan du lära dig att hämta den aktuella uppsättningen av rekommendationer från alla Azure-resurser som är associerade med en prenumeration.

Att hämta den aktuella uppsättningen rekommendationer:
``` http
GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Security/tasks?api-version={api-version}
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Skapa begäran  

Den `{subscription-id}` parametern krävs och måste innehålla prenumerations-ID för Azure-prenumerationen definierar principer. Om du har flera prenumerationer, se [arbeta med flera prenumerationer](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions).  

Den `api-version` parametern är obligatorisk. Dessa slutpunkter för tillfället stöds enbart för `api-version=2015-06-01-preview`. 

Följande huvuden krävs: 

|Huvudet i begäran|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange till `application/json`.|  
|*Auktorisering:*|Krävs. Ange att en giltig `Bearer` [åtkomsttoken](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients). |  

## <a name="response"></a>Svar  

Statuskoden 200 returneras (OK) för ett lyckat svar, som innehåller en lista över rekommenderade åtgärder för att skydda dina Azure-resurser.

``` json
{  
  "value": [  
    {  
       "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
       "name": "{task_id}",
       "type": "Microsoft.Security/locations/{region}/tasks",
       "properties": {
       "state": "Active",
       "subState": "NA",
       "creationTimeUtc": "{create-time}",
       "lastStateChangeTimeUtc": "{last-state-change}",
       "securityTaskParameters": "{security-task-properties}"
    } 
  ]  
}  
```  

Varje objekt i **värdet** representerar en rekommendation:

|Egenskapen svar|Beskrivning|
|----------------|----------|
|**Tillstånd** | Anger om rekommendation är `active` eller `resolved`. |
|**CreationTimeUtc** | Datum och tid i UTC, visar när rekommendationen har skapats. |
|**lastStateChangeUtc** | Datum och tid i UTV för det senaste tillståndet ändras, om sådana finns. |
|**securityTaskParameters** | Information rekommendation. Egenskaper kan variera beroende på den underliggande rekommendationen. |
||
  
För närvarande stöds rekommendationer finns [implementera säkerhetsrekommendationer](https://docs.microsoft.com/azure/security-center/security-center-recommendations).

Andra statuskoder indikerar fel. I dessa fall innehåller objektet svaret en beskrivning som förklarar varför begäran misslyckades.

``` json
{  
  "value": [  
    {  
      "description": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="example-response"></a>Exempelsvar  

``` json
{  
  "value": [  
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{region}/tasks/{task-id}",
            "name": "{task_id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "vmId": "/subscriptions/{subscription-id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "vmName": "{vm_name}",
                    "severity": "{severity}",
                    "isOsDiskEncrypted": {is_os_disk_encrypted},
                    "isDataDiskEncrypted": {is_data_disk_encrypted},
                    "name": "EncryptionOnVm",
                    "uniqueKey": "EncryptionOnVmTaskParameters_/subscriptions/{subscription-id}/resourceGroups/{resoource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}",
                    "resourceId": "/subscriptions/{subscription_id}/resourceGroups/{resource_group}/providers/Microsoft.Compute/virtualMachines/{vm_name}"
                }
            }
        },
        {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Security/locations/{location}/tasks/{task-id}",
            "name": "{task-id}",
            "type": "Microsoft.Security/locations/{region}/tasks",
            "properties": {
                "state": "Active",
                "subState": "NA",
                "creationTimeUtc": "{create-time}",
                "lastStateChangeTimeUtc": "{last-state-change}",
                "securityTaskParameters": {
                    "serverName": "{sql-server-name}",
                    "serverId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}",
                    "name": "Enable auditing for the SQL server",
                    "uniqueKey": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}/auditingPolicies/Default",
                    "resourceId": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Sql/servers/{server-id}"
                }
            }
        }  ]  
}  
```  

Svaret visar två rekommendationer; varje objekt i listan motsvarar en viss rekommendation. Först rekommenderar kryptera lagring på en virtuell Linux-dator och andra föreslår att du aktiverar granskning för en SQLServer.

Rekommendationerna variera beroende på de principer som du har aktiverat. Mer information, inklusive rekommendationer för tillfället finns [hantera säkerhetsrekommendationer i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations).


## <a name="see-also"></a>Se också  
- [Ange säkerhetsprinciper](https://docs.microsoft.com/azure/security-center/security-center-policies-overview)
- [Azure Security Resource provider REST API](https://msdn.microsoft.com/library/azure/mt704034.aspx)   
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
- [PowerShell-modulen för Azure Security Center](https://www.powershellgallery.com/packages/Azure-Security-Center/0.0.22)
