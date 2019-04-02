---
title: Hur du använder rollbaserad åtkomstkontroll i Azure API Management | Microsoft Docs
description: Lär dig hur du använder de inbyggda rollerna och skapa anpassade roller i Azure API Management
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
ms.openlocfilehash: 2e53b0d582a69e10de22e85720833800d44058e3
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793874"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Hur du använder rollbaserad åtkomstkontroll i Azure API Management

Azure API Management bygger på rollbaserad åtkomstkontroll (RBAC) Aktivera detaljerad åtkomsthantering för API Management-tjänster och enheter (till exempel API: er och principer). Den här artikeln ger en översikt över de inbyggda och anpassade rollerna i API Management. Mer information om åtkomsthantering i Azure-portalen finns i [Kom igång med åtkomsthantering i Azure portal](https://azure.microsoft.com/documentation/articles/role-based-access-control-what-is/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Inbyggda roller

API Management för närvarande har tre inbyggda roller och lägger till två fler roller inom en snar framtid. Dessa roller kan tilldelas med olika omfång, inklusive prenumeration, resursgrupp och enskilda API Management-instans. Till exempel om du tilldelar en användare på resursgruppsnivå rollen ”Azure API Management-tjänsten läsare”, har sedan användaren läsbehörighet till alla API Management-instanser i resursgruppen. 

I följande tabell innehåller korta beskrivningar av de inbyggda rollerna. Du kan tilldela dessa roller med hjälp av Azure-portalen eller andra verktyg, inklusive Azure [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell), [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli), och [REST API](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest). Mer information om hur du tilldelar inbyggda roller finns i [använda rolltilldelningar för att hantera åtkomst till din Azure-prenumerationsresurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

| Roll          | Läsbehörighet<sup>[1]</sup> | Skrivåtkomst<sup>[2]</sup> | Skapa tjänster, borttagning, skalning, VPN och konfiguration av anpassad domän | Åtkomst till äldre publisher-portalen | Beskrivning
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Azure API Management-Tjänstdeltagare | ✓ | ✓ | ✓ | ✓ | Superanvändare. Har fullständig CRUD-åtkomst till API Management-tjänster och entiteter (till exempel API: er och principer). Har åtkomst till äldre publisher-portalen. |
| Azure API Management-tjänsten läsare | ✓ | | || Har skrivskyddad åtkomst till API Management-tjänster och entiteter. |
| Azure API Management-tjänsten Operator | ✓ | | ✓ | | Hantera API Management-tjänster, men inte enheter.|
| Azure API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Hantera API Management-entiteter, men inte services.|
| Azure API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kan hantera developer-portalen. Skrivskyddad åtkomst till tjänster och entiteter.|

<sup>[1] läsbehörighet till API Management-tjänster och enheter (till exempel API: er och principer).</sup>

<sup>[2] skrivåtkomst till API Management-tjänster och entiteter förutom följande åtgärder: instans skapas, tas bort eller skalning; VPN-konfigurationen. och installationen av anpassad domän.</sup>

<sup>\* Rollen Service Editor blir tillgänglig när vi har migrerat alla administratören Användargränssnittet från befintliga publisher-portalen till Azure-portalen. Rollen Innehållshanterare blir tillgänglig när publisher-portalen har omstrukturerats så att den bara innehåller funktioner som rör hantering av developer-portalen.</sup>  

## <a name="custom-roles"></a>Anpassade roller

Om ingen av de inbyggda rollerna uppfyller dina specifika behov kan kan anpassade roller skapas för att ge mer detaljerad åtkomsthantering för API Management-entiteter. Du kan till exempel skapa en anpassad roll som har skrivskyddad åtkomst till en API Management-tjänsten, men endast har skrivåtkomst till ett visst API. Läs mer om anpassade roller i [anpassade roller i Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/custom-roles). 

> [!NOTE]
> För att kunna se en API Management-instans i Azure-portalen, en anpassad roll måste innehålla den ```Microsoft.ApiManagement/service/read``` åtgärd.

När du skapar en anpassad roll, är det enklare att börja med någon av de inbyggda rollerna. Redigera attribut att lägga till **åtgärder**, **NotActions**, eller **AssignableScopes**, och sedan spara ändringarna som en ny roll. I följande exempel börjar med rollen ”Azure API Management-tjänsten läsare” och skapar en anpassad roll som kallas ”Calculator API Editor”. Du kan tilldela den anpassade rollen till ett visst API. Därför har den här rollen endast åtkomst till det API: et. 

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

Den [Azure Resource Manager åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) artikeln innehåller listan över behörigheter som kan beviljas för API Management-nivå.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Nästa steg

Mer information om rollbaserad åtkomstkontroll i Azure finns i följande artiklar:
  * [Kom igång med åtkomsthantering i Azure-portalen](../role-based-access-control/overview.md)
  * [Använda rolltilldelningar för att hantera åtkomsten till dina Azure-prenumerationsresurser](../role-based-access-control/role-assignments-portal.md)
  * [Anpassade roller i Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager åtgärder för resursprovider](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)