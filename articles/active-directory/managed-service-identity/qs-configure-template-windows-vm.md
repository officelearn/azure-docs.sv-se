---
title: Hur du konfigurerar hanterad tjänstidentitet på en Azure-dator med hjälp av en mall
description: Stegvisa instruktioner för att konfigurera en hanterad tjänstidentitet på en Azure-dator med en Azure Resource Manager-mall.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 15a743f524c58e56247ec46fee27611b33595bad
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258702"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurera en virtuell dator hanterad tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterad tjänstidentitet på en Azure-dator med hjälp av Azure Resource Manager-mall för distribution:

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell dator och aktivera och ta bort system och/eller användare tilldelas hanterad identitet från en Azure-dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en virtuell dator.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar tillhandahåller möjligheten att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. Aktiverar en system- eller Användartilldelad identitet på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) till distributioner.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet ska du aktivera och inaktivera en systemtilldelad identitet med en Azure Resource Manager-mall.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera systemtilldelad identitet när du skapar en Azure-dator eller på en befintlig virtuell dator

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Efter inläsning av mallen i ett redigeringsprogram för att leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Själv kan skilja sig från följande skärmbild, beroende på redigerare som du använder och om du redigerar en mall för en ny distribution eller befintlig.

   >[!NOTE] 
   > Det här exemplet förutsätter variabler som `vmName`, `storageAccountName`, och `nicName` har definierats i mallen.
   >

   ![Skärmbild av mall - Leta upp virtuell dator](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Om du vill aktivera systemtilldelad identitet, lägger du till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Valfritt) Lägg till hanterad tjänstidentitet för VM-tillägget som en `resources` element. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.  Använd följande syntax:

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

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Tilldela en roll till den Virtuella datorns systemtilldelade identiteter

När du har aktiverat systemtilldelade identiteter på den virtuella datorn kan du behöva ge det en roll som **läsare** åtkomst till resursgruppen där den skapades.

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.
 
2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och Lägg till följande information för att ge din virtuella dator **läsare** åtkomst till resursgruppen där den skapades.  Mallstruktur kan variera beroende på redigeraren och den distributionsmodell som du väljer.
   
   Under den `parameters` avsnittet Lägg till följande:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Under den `variables` avsnittet Lägg till följande:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Under den `resources` avsnittet Lägg till följande:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Inaktivera systemtilldelad identitet från en Azure virtuell dator

Om du har en virtuell dator som inte längre behövs en hanterad tjänstidentitet:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som har bara systemtilldelade identiteter kan du inaktivera det genom att ändra identitetstypen till `None`.  Om den virtuella datorn har både system och användartilldelade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med den `identityIds` matris med användartilldelade identiteter.  I följande exempel visas hur bort en systemtilldelad identitet från en virtuell dator med inga användartilldelade identiteter:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>Användartilldelad identitet

I det här avsnittet ska tilldela du en Användartilldelad identitet till en Azure virtuell dator med hjälp av Azure Resource Manager-mall.

> [!Note]
> För att skapa en Användartilldelad identitet med en Azure Resource Manager-mall, se [skapar du en Användartilldelad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Tilldela Användartilldelad identitet till en Azure virtuell dator

1. Under den `resources` element, Lägg till följande post om du vill tilldela en Användartilldelad identitet till den virtuella datorn.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på Användartilldelad identitet som du skapade.
   
   > [!Important]
   > Den `<USERASSIGNEDIDENTITYNAME>` värdet som visas i följande exempel måste lagras i en variabel.  Dessutom måste stöds för närvarande implementeringen av tilldelas en virtuell dator i en Resource Manager-mall användartilldelade identiteter, api-versionen överens med versionen i följande exempel.
    
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
            ]
        },
    ```
    
2. (Valfritt) Sedan under den `resources` element, Lägg till följande post om du vill tilldela hanterad identitet tillägget till den virtuella datorn. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt. Använd följande syntax:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2015-05-01-preview",
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
            }
        }
    }
    ```
    
3.  När du är klar bör se din mall ut ungefär så här:

      ![Skärmbild av Användartilldelad identitet](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Ta bort Användartilldelad identitet från en Azure-dator

Om du har en virtuell dator som inte längre behövs en hanterad tjänstidentitet:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som bara har Användartilldelad identitet kan du inaktivera det genom att ändra den identitetstypen till `None`.  Om den virtuella datorn har både system och användartilldelade identiteter och du vill behålla systemtilldelade identiteter, ta bort `UserAssigned` från identitetstypen tillsammans med den `identityIds` matris med användartilldelade identiteter.
    
   Ta bort en en enda Användartilldelad identitet från en virtuell dator, ta bort den från den `identityIds` matris.
   
   I följande exempel visas hur bort alla användartilldelade identiteter från en virtuell dator med inga systemtilldelade identiteter:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="related-content"></a>Relaterat innehåll

- Ett bredare perspektiv om hanterad tjänstidentitet läsa den [hanterad tjänstidentitet översikt](overview.md).

