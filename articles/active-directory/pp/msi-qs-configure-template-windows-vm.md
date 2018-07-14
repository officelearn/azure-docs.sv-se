---
title: Hur du konfigurerar en Användartilldelad MSI för en Azure-dator med hjälp av en Azure-mall
description: Steg för steg-instruktioner för hur du konfigurerar en Användartilldelad hanterad tjänstidentitet (MSI) för en Azure-dator med en Azure Resource Manager-mall.
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1af9770fa899a03e6e3514c539c511ba26fdced4
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036388"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Konfigurera en Användartilldelad hanterad tjänstidentitet (MSI) för en virtuell dator med hjälp av en Azure-mall

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till tjänster som stöder Azure AD-autentisering, utan att behöva autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort en Användartilldelad MSI för en Azure-dator med hjälp av en Distributionsmall av Azure Resource Manager.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera MSI under skapandet av en Azure-dator eller på en befintlig virtuell dator

Som med Azure ger portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. Skapa och tilldela en Användartilldelad MSI-dator till en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) distributioner:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller MSI och VM. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på prenumerations- eller resurser (till exempel rollen ”ägare”).

2. Efter inläsning av mallen i ett redigeringsprogram för att leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Själv kan skilja sig från följande skärmbild, beroende på redigerare som du använder och om du redigerar en mall för en ny distribution eller befintlig.

   >[!NOTE] 
   > Det här exemplet förutsätter variabler som `vmName`, `storageAccountName`, och `nicName` har definierats i mallen.
   >

   ![Skärmbild av mall - Leta upp virtuell dator](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Lägg till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Lägg sedan till VM MSI-tillägget som en `resources` element. Använd följande syntax:

   >[!NOTE] 
   > I följande exempel förutsätter att en Windows VM-tillägg (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
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

5. När du är klar bör se din mall ut ungefär så här:

   ![Skärmbild av mall efter uppdateringen](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Ta bort MSI från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver en MSI:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel rollen ”virtuell Datordeltagare”).

2. Ta bort de två element som har lagts till i föregående avsnitt: Virtuellt datorns `"identity"` egenskapen och `"Microsoft.Compute/virtualMachines/extensions"` resurs.

## <a name="related-content"></a>Relaterat innehåll

- Ett bredare perspektiv om MSI läsa den [hanterad tjänstidentitet översikt](msi-overview.md).

