---
title: Skapa och ta bort en Användartilldelad hanterad identitet med hjälp av Azure Resource Manager
description: Steg för steg-anvisningar om hur du skapar och tar bort Användartilldelad hanterade identiteter med Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 9e1ea4e35c1d8b90aa3d0fdf5e619f7b7f7db400
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47107047"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Skapa, visa och ta bort en Användartilldelad hanterad identitet med Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln skapar du en Användartilldelad hanterad identitet med en Azure Resource Manager.

Det går inte att lista och ta bort en Användartilldelad hanterad identitet med en Azure Resource Manager-mall.  Se följande artiklar för att skapa och listan som en Användartilldelad hanterad identitet:

- [Lista användartilldelade hanterad identitet](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Ta bort Användartilldelad hanterad identitet](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Om du vill utföra åtgärder i den här artikeln, måste ditt konto följande rolltilldelningen:
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) rollen kan skapa, läsa (lista), uppdatera och ta bort en hanterad Användartilldelad identitet.

## <a name="template-creation-and-editing"></a>Skapa mallar och redigera

Som med Azure ger portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

- Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
- Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
- Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall. 

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Använd följande mall för att skapa en hanterad Användartilldelad identitet. Ersätt den `<USER ASSIGNED IDENTITY NAME>` värde med dina egna värden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Nästa steg

Information om hur du tilldelar en Användartilldelad-hanterad identitet till en Azure-dator med hjälp av en mall finns i Azure Resource Manager [konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en mallar](qs-configure-template-windows-vm.md).


 
