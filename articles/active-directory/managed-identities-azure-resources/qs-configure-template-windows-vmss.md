---
title: Konfigurera mall för att använda hanterade identiteter på skaluppsättningar för virtuella datorer - Azure AD
description: Steg-för-steg-instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en skalningsuppsättning för virtuella datorer med hjälp av en Azure Resource Manager-mall.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425569"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurera hanterade identiteter för Azure-resurser på en Azure-skala för virtuella datorer med hjälp av en mall

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, utan att ha autentiseringsuppgifter i koden.

I den här artikeln får du lära dig hur du utför följande hanterade identiteter för Azure-resurser åtgärder på en Azure virtuell dator skalningsuppsättning, med hjälp av Azure Resource Manager distributionsmall:
- Aktivera och inaktivera den systemtilldelade hanterade identiteten på en Azure-skalningsuppsättning för virtuella datorer
- Lägga till och ta bort en användartilldelad hanterad identitet på en Azure-skalningsuppsättning för virtuella datorer

## <a name="prerequisites"></a>Krav

- Om du inte känner till hanterade identiteter för Azure-resurser kan du läsa [översiktsavsnittet](overview.md). **Var noga med att granska [skillnaden mellan en systemtilldelad och användartilldelad hanterad identitet](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- För att utföra hanteringsåtgärderna i den här artikeln behöver ditt konto följande Azure-rollbaserade åtkomstkontrolltilldelningar:

    > [!NOTE]
    > Inga ytterligare Azure AD-katalogrolltilldelningar krävs.

    - [Deltagare i virtuella](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) datorer för att skapa en skaluppsättning för virtuella datorer och aktivera och ta bort system- och/eller användartilldelade hanterade identitet från en skaluppsättning för virtuella datorer.
    - [Hanterad identitetsbidragstagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en användartilldelade hanterad identitet.
    - [Hanterad identitetsoperatorroll](/azure/role-based-access-control/built-in-roles#managed-identity-operator) för att tilldela och ta bort en användartilldelad hanterad identitet från och till en skaluppsättning för virtuella datorer.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager-mallar](../../azure-resource-manager/management/overview.md) ger möjlighet att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för mallredigering och distribution, både lokala och portalbaserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från grunden, eller basera den på en befintlig gemensam mall eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Härleda från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/templates/export-template-portal.md)eller från [det aktuella tillståndet för distributionen](../../azure-resource-manager/templates/export-template-portal.md).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md)och sedan ladda upp och distribuera med powershell eller CLI.
   - Använda Projektet Visual Studio [Azure Resource Group](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) för att både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer är mallsyntaxen densamma under den första distributionen och omdistributionen. Aktivera hanterade identiteter för Azure-resurser på en ny eller befintlig virtuell dator görs på samma sätt. Som standard gör Azure Resource Manager också en [inkrementell uppdatering](../../azure-resource-manager/templates/deployment-modes.md) av distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelad hanterad identitet

I det här avsnittet aktiverar och inaktiverar du den systemtilldelade hanterade identiteten med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterade identitet när du skapar en skalauppsättning för virtuella datorer eller en befintlig skaluppsättning för virtuella datorer

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn.
2. Om du vill aktivera den systemtilldelade hanterade identiteten `Microsoft.Compute/virtualMachinesScaleSets` läser du in mallen i `identity` en redigerare, `"type": "Microsoft.Compute/virtualMachinesScaleSets"` letar reda på intresseresursen i resursavsnittet och lägger till egenskapen på samma nivå som egenskapen. Använd följande syntax:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Du kan också etablera hanterade identiteter för Azure-resurser virtuell dator `extensionProfile` skalningsuppsättning tillägg genom att ange det i elementet i mallen. Det här steget är valfritt eftersom du kan använda imds-identitetsslutpunkten (Azure Instance Metadata Service) för att hämta token.  Mer information finns i [Migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).


4. När du är klar bör följande avsnitt läggas till i resursavsnittet i mallen och bör likna följande:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en systemtilldelad hanterad identitet från en Azure-skalningsuppsättning för virtuella datorer

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en systemtilldelad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn.

2. Läs in mallen i `Microsoft.Compute/virtualMachineScaleSets` en [redigerare](#azure-resource-manager-templates) och hitta den resurs som är av intresse i avsnittet. `resources` Om du har en virtuell dator som bara har systemtilldelad hanterad `None`identitet kan du inaktivera den genom att ändra identitetstypen till .

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion är `2018-06-01` och den virtuella datorn har både `SystemAssigned` system- och `UserAssigned` användartilldelade hanterade identiteter tar du bort från identitetstypen och håller jämna steg med listlistevärdena userAssignedId.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion `2017-12-01` är och din virtuella dator skaluppsättning har både `SystemAssigned` system- och `UserAssigned` användartilldelade `identityIds` hanterade identiteter, ta bort från identitetstypen och hålla tillsammans med matrisen för de användartilldelade hanterade identiteterna.



   I följande exempel visas hur du tar bort en systemtilldelad hanterad identitet från en skaluppsättning för virtuella datorer utan användartilldelade hanterade identiteter:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Användartilldelad hanterad identitet

I det här avsnittet tilldelar du en användartilldelade hanterad identitet till en skalauppsättning för virtuella datorer med Azure Resource Manager-mallen.

> [!Note]
> Information om hur du skapar en användartilldelad hanterad identitet med hjälp av en Azure Resource Manager-mall finns i [Skapa en användartilldelad hanterad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Tilldela en användartilldelad hanterad identitet till en skaluppsättning för virtuella datorer

1. Lägg `resources` till följande post under elementet för att tilldela en användartilldelad hanterad identitet till skaluppsättningen för den virtuella datorn.  Var noga `<USERASSIGNEDIDENTITY>` med att ersätta med namnet på den användartilldelade hanterade identitet som du skapade.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om din apiVersion `2018-06-01`är, dina användartilldelade hanterade `userAssignedIdentities` identiteter `<USERASSIGNEDIDENTITYNAME>` lagras i ordlistan format och `variables` värdet måste lagras i en variabel som definieras i avsnittet i mallen.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**

   Om `apiVersion` du `2017-12-01` är eller tidigare lagras dina användartilldelade `identityIds` hanterade `<USERASSIGNEDIDENTITYNAME>` identiteter i matrisen och värdet måste lagras i en variabel som definieras i variabelavsnittet i mallen.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Du kan också etablera hanterade identiteter för Azure-resurser virtuell dator `extensionProfile` skalningsuppsättning tillägg genom att ange det i elementet i mallen. Det här steget är valfritt eftersom du kan använda imds-identitetsslutpunkten (Azure Instance Metadata Service) för att hämta token.  Mer information finns i [Migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).

3. När du är klar bör mallen se ut ungefär så här:

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API version 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort användartilldelade hanterade identitet från en Azure-skalningsuppsättning för virtuella datorer

Om du har en skalningsuppsättning för den virtuella datorn som inte längre behöver en användartilldelad hanterad identitet:

1. Oavsett om du loggar in på Azure lokalt eller via Azure-portalen använder du ett konto som är associerat med Azure-prenumerationen som innehåller skalningsuppsättningen för den virtuella datorn.

2. Läs in mallen i `Microsoft.Compute/virtualMachineScaleSets` en [redigerare](#azure-resource-manager-templates) och hitta den resurs som är av intresse i avsnittet. `resources` Om du har en skalningsuppsättning för virtuella datorer som bara har användartilldelade `None`hanterade identitet kan du inaktivera den genom att ändra identitetstypen till .

   I följande exempel visas hur du tar bort alla användartilldelade hanterade identiteter från en virtuell dator utan systemtilldelade hanterade identiteter:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Om du vill ta bort en enda användartilldelad hanterad `userAssignedIdentities` identitet från en skaluppsättning för virtuella datorer tar du bort den från ordlistan.

   Om du har en systemtilldelad identitet behåller `type` du `identity` den i värdet under värdet.

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**

   Om du vill ta bort en enda användartilldelad hanterad `identityIds` identitet från en skaluppsättning för virtuella datorer tar du bort den från matrisen.

   Om du har en systemtilldelad hanterad identitet `type` behåller `identity` du den i värdet under värdet.

## <a name="next-steps"></a>Nästa steg

- [Hanterad identitet för Azure-resursöversikt](overview.md).
