---
title: Så här använder du rollbaserad åtkomstkontroll i Azure API Management | Microsoft-dokument
description: Lär dig hur du använder de inbyggda rollerna och skapar anpassade roller i Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: ed0cd51fc686735f2d9c110ce46d5904107cafc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430622"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Så här använder du rollbaserad åtkomstkontroll i Azure API Management

Azure API Management förlitar sig på RBAC (Azure Role-Based Access Control) för att möjliggöra finkornig åtkomsthantering för API Management-tjänster och entiteter (till exempel API:er och principer). Den här artikeln ger dig en översikt över de inbyggda och anpassade rollerna i API Management. Mer information om åtkomsthantering i Azure-portalen finns [i Komma igång med åtkomsthantering i Azure-portalen](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Inbyggda roller

API Management ger för närvarande tre inbyggda roller och kommer att lägga till ytterligare två roller inom en snar framtid. Dessa roller kan tilldelas vid olika scope, inklusive prenumeration, resursgrupp och enskilda API Management-instans. Om du till exempel tilldelar rollen "API Management Service Reader" till en användare på resursgruppsnivå, har användaren läsbehörighet till alla API Management-instanser i resursgruppen. 

I följande tabell finns korta beskrivningar av de inbyggda rollerna. Du kan tilldela dessa roller med hjälp av Azure-portalen eller andra verktyg, inklusive Azure [PowerShell,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Mer information om hur du tilldelar inbyggda roller finns i [Använda rolltilldelningar för att hantera åtkomst till dina Azure-prenumerationsresurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Roll          | Läsåtkomst<sup>[1]</sup> | Skrivåtkomst<sup>[2]</sup> | Skapande, borttagning, skalning, VPN och anpassad domänkonfiguration | Tillgång till den äldre utgivarens portal | Beskrivning
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API-hanteringstjänstdeltagare | ✓ | ✓ | ✓ | ✓ | Super användare. Har fullständig CRUD-åtkomst till API Management-tjänster och enheter (till exempel API:er och principer). Har åtkomst till den äldre utgivarportalen. |
| API Management Service Reader | ✓ | | || Har skrivskyddad åtkomst till API Management-tjänster och entiteter. |
| Operatör för API-hanteringstjänster | ✓ | | ✓ | | Kan hantera API Management-tjänster, men inte entiteter.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kan hantera API Management-entiteter, men inte tjänster.|
| Innehållshanteraren för API-hantering<sup>*</sup> | ✓ | | | ✓ | Kan hantera utvecklarportalen. Skrivskyddad åtkomst till tjänster och entiteter.|

<sup>[1] Läs åtkomst till API Management-tjänster och enheter (till exempel API:er och principer).</sup>

<sup>[2] Skrivåtkomst till API Management-tjänster och entiteter utom följande åtgärder: skapande, borttagning och skalning av instanser. VPN-konfiguration; och anpassad domäninstallation.</sup>

<sup>\*Rollen Tjänstredigerare är tillgänglig när vi har migrerat alla administratörsgränssnitt från den befintliga utgivarportalen till Azure-portalen. Rollen Content Manager blir tillgänglig när utgivarportalen har återfactorerats för att endast innehålla funktioner som är relaterade till hanteringen av utvecklarportalen.</sup>  

## <a name="custom-roles"></a>Anpassade roller

Om ingen av de inbyggda rollerna uppfyller dina specifika behov kan anpassade roller skapas för att tillhandahålla mer detaljerad åtkomsthantering för API Management-entiteter. Du kan till exempel skapa en anpassad roll som har skrivskyddad åtkomst till en API Management-tjänst, men som bara har skrivbehörighet till ett specifikt API. Mer information om anpassade roller finns [i Anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> För att kunna se en API Management-instans i Azure-portalen måste en anpassad roll innehålla ```Microsoft.ApiManagement/service/read``` åtgärden.

När du skapar en anpassad roll är det enklare att börja med en av de inbyggda rollerna. Redigera attributen för att lägga till **åtgärder,** **NotActions**eller **AssignableScopes**och spara ändringarna som en ny roll. Följande exempel börjar med rollen "API Management Service Reader" och skapar en anpassad roll som heter "Calculator API Editor". Du kan tilldela den anpassade rollen till ett visst API. Den här rollen har därför bara åtkomst till det API:et. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Artikeln [för Azure Resource Manager-resursproviderns verksamhet](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) innehåller en lista över behörigheter som kan beviljas på API Management-nivån.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nästa steg

Mer information om rollbaserad åtkomstkontroll i Azure finns i följande artiklar:
  * [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
  * [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)
  * [Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager-resursprovideråtgärder](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
