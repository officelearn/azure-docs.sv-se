---
title: Skapa och ta bort en användare tilldelas hanterad tjänstidentitet med Azure Resource Manager
description: Steg för steg tilldelas instruktioner om hur du skapar och tar bort användaren hanterad tjänstidentitet med hjälp av Azure-resurs.
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
ms.openlocfilehash: a7ddcb834b135d2177355a0523c7e99bcc599e99
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931522"
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Skapa, visa och ta bort en Användartilldelad identitet med Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln skapar du en användare som tilldelats hanterad identitet med en Azure Resource Manager.

Det går inte att lista och ta bort en Användartilldelad identitet med en Azure Resource Manager-mall.  Se följande artiklar för att skapa och visa en Användartilldelad identitet:

- [Lista Användartilldelad identitet](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Ta bort Användartilldelad identitet](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel rollen ”virtuell Datordeltagare”).

## <a name="template-creation-and-editing"></a>Skapa mallar och redigera

Som med Azure ger portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

- Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
- Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
- Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall. 

## <a name="create-a-user-assigned-identity"></a>Skapa en Användartilldelad identitet 

Använd följande mall för att skapa en Användartilldelad identitet. Ersätt den `<USER ASSIGNED IDENTITY NAME>` värde med dina egna värden:

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

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
## <a name="related-content"></a>Relaterat innehåll

Information om hur du tilldelar en Användartilldelad identitet till en Azure-dator med hjälp av en mall finns i Azure Resource Manager [konfigurera en virtuell dator hanterad tjänstidentitet med hjälp av en mall](qs-configure-template-windows-vm.md).


 
