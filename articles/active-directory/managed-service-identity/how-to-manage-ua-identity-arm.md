---
title: Hur du skapar och tar bort en användare som tilldelats hanterade tjänstidentiteten med Azure Resource Manager
description: Steg för steg tilldelas instruktioner om hur du skapar och tar bort användare hanterade tjänstidentiteten som använder Azure-resurs.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: 96fd86a2af3372f53973c95ee11ab35ccee1e63e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Skapa, visa och ta bort en tilldelad användaridentitet med Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln skapar du en användare som tilldelats hanterade identitet med hjälp av en Azure Resource Manager.

Det går inte att visa och ta bort en användare som tilldelats identitet med hjälp av en Azure Resource Manager-mall.  Se följande artiklar för att skapa och visa en lista med en tilldelad användaridentitet:

- [Lista över användare som tilldelats identitet](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [Ta bort användaren som har tilldelats identitet](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

Om du loggar in till Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel roll ”Virtual Machine-deltagare”).

## <a name="template-creation-and-editing"></a>Skapa mallar och redigering

Som i Azure ge portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

- Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller baseras på en befintlig gemensamma eller [QuickStart mallen](https://azure.microsoft.com/documentation/templates/).
- Härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen av](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md), och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
- Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) både skapa och distribuera en mall. 

## <a name="create-a-user-assigned-identity"></a>Skapa en användare som tilldelats identitet 

Använd följande mall för att skapa en användare som tilldelats identitet. Ersätt den `<USER ASSIGNED IDENTITY NAME>` värdet med egna värden:

> [!IMPORTANT]
> Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

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
      "name": "[parameters('<USER ASSIGNED IDENTITY NAME>')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('<USER ASSIGNED IDENTITY NAME>')]"
      }
  }
}
```
## <a name="related-content"></a>Relaterat innehåll

Mer information om hur du tilldelar en identitet för användaren som har tilldelats till en virtuell Azure-dator med hjälp av en mall Se Azure Resource Manager [konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall](qs-configure-template-windows-vm.md).


 
