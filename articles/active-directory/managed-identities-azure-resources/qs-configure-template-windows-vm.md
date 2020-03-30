---
title: Konfigurera hanterade identiteter på Azure VM med mallen - Azure AD
description: Steg-för-steg-instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med hjälp av en Azure Resource Manager-mall.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972153"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Konfigurera hanterade identiteter för Azure-resurser på en Virtuell Azure-dator med hjälp av en mall

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden.

I den här artikeln, med hjälp av distributionsmallen för Azure Resource Manager, lär du dig hur du utför följande hanterade identiteter för Azure-resurser på en Virtuell Azure:In this article, using the Azure Resource Manager deployment template, you learn how to perform the following managed identities for Azure resources operations on an Azure VM:

## <a name="prerequisites"></a>Krav

- Om du inte kan använda distributionsmallen för Azure Resource Manager kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager-mallar](../../azure-resource-manager/management/overview.md) ger möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för mallredigering och distribution, både lokala och portalbaserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från grunden, eller basera den på en befintlig gemensam mall eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Härleda från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/templates/export-template-portal.md)eller från [det aktuella tillståndet för distributionen](../../azure-resource-manager/templates/export-template-portal.md).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md)och sedan ladda upp och distribuera med powershell eller CLI.
   - Använda Projektet Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för att både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer är mallsyntaxen densamma under den första distributionen och omdistributionen. Aktivera en hanterad system eller användartilldelade hanterad identitet på en ny eller befintlig virtuell dator görs på samma sätt. Som standard gör Azure Resource Manager också en [inkrementell uppdatering](../../azure-resource-manager/templates/deployment-modes.md) av distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet aktiverar och inaktiverar du en systemtilldelad hanterad identitet med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera systemtilldelade hanterade identiteter när en Virtuell Azure skapas eller på en befintlig virtuell dator

För att aktivera systemtilldelade hanterade identitet på en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

2. Om du vill aktivera systemtilldelade hanterade identitet läser `Microsoft.Compute/virtualMachines` du in mallen i en redigerare, letar reda på intresseresursen `resources` i avsnittet och lägger till egenskapen `"identity"` på samma nivå som egenskapen. `"type": "Microsoft.Compute/virtualMachines"` Använd följande syntax:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. När du är klar bör följande avsnitt `resource` läggas till i avsnittet i mallen och det bör likna följande:

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

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
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
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Tilldela en roll den virtuella datorns systemtilldelade hanterade identitet

När du har aktiverat den systemtilldelade hanterade identiteten på den virtuella datorn kanske du vill ge den en roll som **läsaråtkomst** till resursgruppen där den skapades.

Om du vill tilldela en roll till den virtuella datorns systemtilldelade identitet behöver ditt konto rolltilldelningen [för administratör för användaråtkomst.](/azure/role-based-access-control/built-in-roles#user-access-administrator)

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läs in mallen i en [redigerare](#azure-resource-manager-templates) och lägg till följande information för att ge **vm-läsaren** åtkomst till resursgruppen där den skapades.  Mallstrukturen kan variera beroende på vilken redigerare och distributionsmodell du väljer.

   Under `parameters` avsnittet lägg till följande:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Under `variables` avsnittet lägg till följande:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Under `resources` avsnittet lägg till följande:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Inaktivera en systemtilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort den systemtilldelade hanterade identiteten från en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läs in mallen i `Microsoft.Compute/virtualMachines` en [redigerare](#azure-resource-manager-templates) och hitta den resurs som är av intresse i avsnittet. `resources` Om du har en virtuell dator som bara har systemtilldelad hanterad `None`identitet kan du inaktivera den genom att ändra identitetstypen till .  

   **Microsoft.Compute/virtualMachines API version 2018-06-01**

   Om den virtuella datorn har både system- `SystemAssigned` och användartilldelade `UserAssigned` hanterade identiteter tar du bort från identitetstypen och håller jämna steg med `userAssignedIdentities` ordlistevärdena.

   **Microsoft.Compute/virtualMachines API version 2018-06-01**

   Om `apiVersion` din `2017-12-01` är och din virtuella dator har både `SystemAssigned` system- och användartilldelade hanterade identiteter tar du bort från identitetstypen och håller `UserAssigned` sig tillsammans med matrisen `identityIds` för de användartilldelade hanterade identiteterna.  

I följande exempel visas hur du tar bort en systemtilldelad hanterad identitet från en virtuell dator utan användartilldelade hanterade identiteter:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet tilldelar du en användartilldelade hanterad identitet till en Azure VM med Azure Resource Manager-mallen.

> [!Note]
> Information om hur du skapar en användartilldelad hanterad identitet med hjälp av en Azure Resource Manager-mall finns i [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Tilldela en användartilldelad hanterad identitet till en Virtuell Azure-dator

Om du vill tilldela en användartilldelning till en virtuell dator behöver ditt konto [rolltilldelningarna för deltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) och [hanterad identitetsoperatör.](/azure/role-based-access-control/built-in-roles#managed-identity-operator) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Lägg `resources` till följande post under elementet för att tilldela en användartilldelad hanterad identitet till den virtuella datorn.  Var noga `<USERASSIGNEDIDENTITY>` med att ersätta med namnet på den användartilldelade hanterade identitet som du skapade.

   **Microsoft.Compute/virtualMachines API version 2018-06-01**

   Om `apiVersion` det `2018-06-01`är lagras dina användartilldelade hanterade `userAssignedIdentities` identiteter `<USERASSIGNEDIDENTITYNAME>` i ordlisteformatet och `variables` värdet måste lagras i en variabel som definieras i avsnittet i mallen.

   ```JSON
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

   **Microsoft.Compute/virtualMachines API version 2017-12-01**

   Om `apiVersion` det `2017-12-01`är lagras dina användartilldelade hanterade `identityIds` identiteter `<USERASSIGNEDIDENTITYNAME>` i matrisen och värdet `variables` måste lagras i en variabel som definieras i avsnittet i mallen.

   ```JSON
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

3. När du är klar bör följande avsnitt `resource` läggas till i avsnittet i mallen och det bör likna följande:

   **Microsoft.Compute/virtualMachines API version 2018-06-01**    

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
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
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
   **Microsoft.Compute/virtualMachines API version 2017-12-01**

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

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
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
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Ta bort en användartilldelad hanterad identitet från en virtuell Azure-dator

Om du vill ta bort en användartilldelningsidentitet från en virtuell dator behöver ditt konto rolltilldelningen för deltagare för [virtuell dator.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller den virtuella datorn.

2. Läs in mallen i `Microsoft.Compute/virtualMachines` en [redigerare](#azure-resource-manager-templates) och hitta den resurs som är av intresse i avsnittet. `resources` Om du har en virtuell dator som bara har användartilldelade hanterade identitet `None`kan du inaktivera den genom att ändra identitetstypen till .

   I följande exempel visas hur du tar bort alla användartilldelade hanterade identiteter från en virtuell dator utan systemtilldelade hanterade identiteter:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API version 2018-06-01**

   Om du vill ta bort en enda användartilldelad `useraAssignedIdentities` hanterad identitet från en virtuell dator tar du bort den från ordlistan.

   Om du har en systemtilldelad hanterad identitet `type` behåller `identity` du den i värdet under värdet.

   **Microsoft.Compute/virtualMachines API version 2017-12-01**

   Om du vill ta bort en enda användartilldelad `identityIds` hanterad identitet från en virtuell dator tar du bort den från matrisen.

   Om du har en systemtilldelad hanterad identitet `type` behåller `identity` du den i värdet under värdet.

## <a name="next-steps"></a>Nästa steg

- [Hanterad identitet för Azure-resursöversikt](overview.md).
