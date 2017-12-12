---
title: "Hur du konfigurerar MSI på en virtuell dator i Azure med hjälp av en mall"
description: "Stegvisa instruktioner för att konfigurera en hanterad tjänst identitet (MSI) på en Azure-dator med en Azure Resource Manager-mall."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 047c4f69ae520c730d063246d41702207c55a0b0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Hanterad Service identitet (MSI) ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du aktiverar och ta bort MSI för en Azure-dator med en Azure Resource Manager-Distributionsmall i den här artikeln.

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera MSI vid skapandet av en virtuell dator i Azure eller på en befintlig virtuell dator

Som i Azure ge portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller baseras på en befintlig gemensamma eller [QuickStart mallen](https://azure.microsoft.com/documentation/templates/).
   - Härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen av](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../azure-resource-manager/resource-manager-create-first-template.md), och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är det under första distributionen och Omdistributionen i mallens syntax. Aktivera MSI på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard, Azure Resource Manager har en [stegvis uppdatering](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) för distributioner:

1. Om du loggar in till Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel roll ”Virtual Machine-deltagare”).

2. Efter att läsa in mallen i en textredigerare, leta upp den `Microsoft.Compute/virtualMachines` resurs av intresse inom den `resources` avsnitt. Din kan skilja sig något från följande skärmbild, beroende på hur du använder redigeraren och om du redigerar en mall för en ny distribution eller befintlig.

   >[!NOTE] 
   > Det här exemplet förutsätter variabler som `vmName`, `storageAccountName`, och `nicName` har definierats i mallen.
   >

   ![Skärmbild av mall - hitta VM](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Lägg till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Lägg sedan till VM MSI-tillägget som en `resources` element. Använd följande syntax:

   >[!NOTE] 
   > Följande exempel förutsätter en Windows VM-tillägget (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

5. När du är klar bör mallen likna följande:

   ![Skärmbild av mallen efter uppdateringen](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en virtuell dator i Azure

Om du har en virtuell dator som inte längre behöver en MSI:

1. Om du loggar in till Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel roll ”Virtual Machine-deltagare”).

2. Ta bort de två element som har lagts till i föregående avsnitt: den virtuella datorn `"identity"` egenskapen och `"Microsoft.Compute/virtualMachines/extensions"` resurs.

## <a name="related-content"></a>Relaterat innehåll

- Ett bredare perspektiv om MSI läsa den [hanterade tjänstidentiteten översikt](msi-overview.md).

