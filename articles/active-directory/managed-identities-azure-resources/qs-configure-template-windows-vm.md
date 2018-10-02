---
title: Hur du konfigurerar hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en mall
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med en Azure Resource Manager-mall.
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
ms.openlocfilehash: f50714538cdfd1a0bd258a3b78d0885e3c7beed1
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018493"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurera hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av mallar

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln med distributionsmallen Azure Resource Manager-du lära dig hur du utför följande hanterade identiteter för Azure-resurser på en Azure virtuell dator:

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hjälp av mall för distribution av Azure Resource Manager kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande Azure rollbaserad åtkomstkontroll tilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD directory rolltilldelningar krävs.

    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell dator och aktivera och ta bort system och/eller användartilldelade hanterad identitet från en Azure-dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad hanterad identitet från och till en virtuell dator.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar tillhandahåller möjligheten att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. Aktiverar en system- eller användartilldelade hanterad identitet på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](../../azure-resource-manager/deployment-modes.md) till distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet ska du aktivera och inaktivera en automatiskt genererad hanterad identitet med en Azure Resource Manager-mall.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera systemtilldelade hanterad identitet när du skapar en Azure-dator eller på en befintlig virtuell dator

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Om du vill aktivera systemtilldelade hanterad identitet, läsa in mallen till en textredigerare, leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` och lägger till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. (Valfritt) Lägg till virtuell dator som hanteras identiteter för tillägget för Azure-resurser som en `resources` element. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.  Använd följande syntax:

   >[!NOTE] 
   > I följande exempel förutsätter att en Windows VM-tillägg (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element. VM-tillägget är planerat för utfasning i januari 2019.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
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

4. När du är klar i följande avsnitt ska läggas till i `resource` avsnitt i mallen och det bör likna följande:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01",
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
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Tilldela en roll till den Virtuella datorns systemtilldelade hanterad identitet

När du har aktiverat systemtilldelade hanterad identitet på den virtuella datorn kan du behöva ge det en roll som **läsare** åtkomst till resursgruppen där den skapades.

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera en automatiskt genererad hanterad identitet från en Azure virtuell dator

Om du har en virtuell dator som inte längre behöver en automatiskt genererad hanterad identitet:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som har bara systemtilldelade hanterad identitet kan du inaktivera det genom att ändra identitetstypen till `None`.  
   
   **Microsoft.Compute/virtualMachines API-versionen 2018-06-01**

   Om den virtuella datorn har både system och användartilldelade hanterade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med den `userAssignedIdentities` ordlista värden.

   **Microsoft.Compute/virtualMachines API-versionen 2018-06-01**
   
   Om din `apiVersion` är `2017-12-01` och den virtuella datorn har både system och användartilldelade hanterade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med den `identityIds` matris med de Användartilldelad hanterade identiteter.  
   
I följande exempel visas hur bort en automatiskt genererad hanterad identitet från en virtuell dator med inga hanterade användartilldelade identiteter:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-managed-identity"></a>Användartilldelade hanterad identitet

I det här avsnittet tilldelar du en hanterad Användartilldelad identitet till en Azure-dator med hjälp av Azure Resource Manager-mall.

> [!Note]
> För att skapa en Användartilldelad hanterad identitet med en Azure Resource Manager-mall, se [skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

 ### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Tilldela en hanterad Användartilldelad identitet till en Azure-dator

1. Under den `resources` element, Lägg till följande post om du vill tilldela en hanterad Användartilldelad identitet till den virtuella datorn.  Se till att ersätta `<USERASSIGNEDIDENTITY>` hanteras med namnet på den Användartilldelad identitet som du skapade.

   **Microsoft.Compute/virtualMachines API-versionen 2018-06-01**

   Om din `apiVersion` är `2018-06-01`, dina hanterade användartilldelade identiteter lagras i den `userAssignedIdentities` ordlista format och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i den `variables` i mallen.

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines API-versionen 2017-12-01**
    
   Om din `apiVersion` är `2017-12-01`, dina hanterade användartilldelade identiteter lagras i den `identityIds` matris och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i den `variables` i mallen.
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. (Valfritt) Sedan under den `resources` element, Lägg till följande post om du vill tilldela hanterad identitet tillägget till den virtuella datorn (planerad för utfasning i januari 2019). Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt. Använd följande syntax:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
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
    
3. När du är klar i följande avsnitt ska läggas till i `resource` avsnitt i mallen och det bör likna följande:
   
   **Microsoft.Compute/virtualMachines API-versionen 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
            "apiVersion": "2018-06-01-preview",
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
    ]
   ```
   **Microsoft.Compute/virtualMachines API-versionen 2017-12-01**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForLinux')]",
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
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en hanterad Användartilldelad identitet från en Azure-dator

Om du har en virtuell dator som inte längre behövs en hanterad Användartilldelad identitet:

1. Om du loggar in till Azure lokalt eller via Azure portal kan du använda ett konto som är associerad med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachines` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som har bara användartilldelade hanterad identitet kan du inaktivera det genom att ändra identitetstypen till `None`.
 
   I följande exempel visas hur bort alla användartilldelade hanterade identiteter från en virtuell dator med inga hanterade systemtilldelade identiteter:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **Microsoft.Compute/virtualMachines API-versionen 2018-06-01**
    
   Om du vill ta bort en enda hanterad användartilldelade-identitet från en virtuell dator, ta bort den från den `useraAssignedIdentities` ordlista.

   Om du har en hanterad identitet med systemtilldelade förvara den på den i den `type` värde den `identity` värde.
 
   **Microsoft.Compute/virtualMachines API-versionen 2017-12-01**

   Om du vill ta bort en enda hanterad användartilldelade-identitet från en virtuell dator, ta bort den från den `identityIds` matris.

   Om du har en hanterad identitet med systemtilldelade förvara den på den i den `type` värde den `identity` värde.
   
## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för Azure-resurser översikt](overview.md).

