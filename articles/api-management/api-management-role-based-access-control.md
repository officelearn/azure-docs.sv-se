---
title: Hur du använder rollbaserad åtkomstkontroll i Azure API Management | Microsoft Docs
description: Lär dig hur du använder inbyggda roller och skapa anpassade roller i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: b16bd5b93eda0fcb0c0c9ff96f86efc866abf798
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294487"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Hur du använder rollbaserad åtkomstkontroll i Azure API Management
Azure API Management förlitar sig på rollbaserad åtkomstkontroll (RBAC) att aktivera detaljerad åtkomsthantering för API Management-tjänster och enheter (till exempel API: er och principer). Den här artikeln ger en översikt över inbyggda och anpassade roller i API-hantering. Mer information om åtkomsthantering i Azure portal finns [Kom igång med åtkomsthantering i Azure portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

## <a name="built-in-roles"></a>Inbyggda roller
API-hantering för närvarande tillhandahåller tre inbyggda roller och lägger till två flera roller i den nära framtiden. Rollerna kan tilldelas på olika omfång, inklusive prenumerationen, resursgruppen och enskilda API Management-instans. Till exempel om du tilldelar rollen ”Azure API Management-tjänsten läsare” till en användare på nivån resursgruppens namn, har sedan användaren läsbehörighet till alla API Management-instanser i resursgruppen. 

Följande tabell innehåller en kort beskrivning av de inbyggda rollerna. Du kan tilldela dessa roller med hjälp av Azure-portalen eller andra verktyg, inklusive Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Mer information om hur du tilldelar inbyggda roller finns [använda rolltilldelningar för att hantera åtkomst till resurserna i Azure-prenumeration](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Roll          | Läsbehörighet<sup>[1]</sup> | Skrivåtkomst<sup>[2]</sup> | Skapa en tjänst, borttagning, skalning, VPN och konfiguration för domänen | Åtkomst till äldre publisher-portalen | Beskrivning
| ------------- | ---- | ---- | ---- | ---- | ---- | ---- |
| Azure API Management-tjänsten deltagare | ✓ | ✓ | ✓ | ✓ | Superanvändare. Har fullständig CRUD-åtkomst till API Management-tjänster och enheter (till exempel API: er och principer). Har åtkomst till den äldra publisher-portalen. |
| Azure API Management-tjänsten läsare | ✓ | | || Har skrivskyddad åtkomst till API Management-tjänster och enheter. |
| Azure API Management-tjänsten Operator | ✓ | | ✓ | | Kan hantera API Management services, men inte enheter.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kan hantera API Management-enheter, men inte tjänster.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kan hantera developer-portalen. Skrivskyddad åtkomst till tjänster och enheter.|

<sup>[1] läsbehörighet till API Management-tjänster och enheter (till exempel API: er och principer).</sup>

<sup>[2] skrivåtkomst till API Management-tjänster och entiteter förutom följande åtgärder: instans skapas, tas bort eller skalning; VPN-konfigurationen. och domänen.</sup>

<sup>\* Rollen Service Editor blir tillgänglig efter vi att migrera alla admin UI från befintliga publisher portal till Azure-portalen. Rollen Innehållshanterare blir tillgänglig efter utgivare portal omstrukturerade så att den bara innehåller funktioner som är relaterade till att hantera developer-portalen.</sup>  

## <a name="custom-roles"></a>Anpassade roller
Om ingen av de inbyggda rollerna uppfyller dina specifika behov kan du skapa anpassade roller för att ge mer detaljerad åtkomsthantering för API Management-enheter. Du kan till exempel skapa en anpassad roll som har skrivskyddad åtkomst till en API Management-tjänsten, men endast har skrivbehörighet till en specifik API. Läs mer om anpassade roller i [anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

När du skapar en anpassad roll, är det lättare att börja med en av de inbyggda rollerna. Redigera de attribut du vill lägga till **åtgärder**, **NotActions**, eller **AssignableScopes**, och sedan spara ändringarna som en ny roll. I följande exempel börjar med rollen ”Azure API Management-tjänsten läsare” och skapar en anpassad roll som kallas ”Kalkylatorn API-redigeraren”. Du kan tilldela den anpassade rollen till specifika API. Den här rollen har därför bara åtkomst till den API. 

```
$role = Get-AzureRmRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzureRmRoleDefinition -Role $role
New-AzureRmRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Den [Azure Resource Manager resource provider operations](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) artikeln innehåller en lista med behörigheter som kan beviljas för API Management-nivå.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nästa steg

Mer information om rollbaserad åtkomstkontroll i Azure finns i följande artiklar:
  * [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
  * [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)
  * [Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Provideråtgärder i Azure Resource Manager resurs](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)

