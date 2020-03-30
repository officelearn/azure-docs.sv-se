---
title: Skapa & ta bort en användartilldelad hanterad identitet med Hjälp av Azure Resource Manager
description: Steg för steg instruktioner om hur du skapar och tar bort användartilldelade hanterade identiteter med Hjälp av Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253382"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Skapa, lista och ta bort en användartilldelad hanterad identitet med Hjälp av Azure Resource Manager


Hanterade identiteter för Azure-resurser ger Azure-tjänster en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i koden. 

I den här artikeln skapar du en användartilldelad hanterad identitet med hjälp av en Azure Resource Manager.

Det går inte att lista och ta bort en användartilldelad hanterad identitet med hjälp av en Azure Resource Manager-mall.  Se följande artiklar för att skapa och lista en användartilldelad hanterad identitet:

- [Lista användartilldelade hanterade identitet](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Ta bort användartilldelade hanterade identitet](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="template-creation-and-editing"></a>Skapa och redigera mall

Precis som med Azure-portalen och skript, Azure Resource Manager-mallar ger möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för mallredigering och distribution, både lokala och portalbaserade, inklusive:

- Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från grunden, eller basera den på en befintlig gemensam mall eller [Snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
- Härleda från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)eller från [det aktuella tillståndet för distributionen](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md)och sedan ladda upp och distribuera med powershell eller CLI.
- Använda Projektet Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för att både skapa och distribuera en mall. 

## <a name="create-a-user-assigned-managed-identity"></a>Skapa en användartilldelad hanterad identitet 

Om du vill skapa en användartilldelad hanterad identitet behöver ditt konto rolltilldelningen [Managed Identity Contributor.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Om du vill skapa en användartilldelad hanterad identitet använder du följande mall. Ersätt `<USER ASSIGNED IDENTITY NAME>` värdet med dina egna värden:

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
      "apiVersion": "2018-11-30",
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

Information om hur du tilldelar en användartilldelade hanterad identitet till en Azure-virtuell dator med hjälp av en Azure Resource Manager-mall finns i [Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med hjälp av en mall .](qs-configure-template-windows-vm.md)


 
