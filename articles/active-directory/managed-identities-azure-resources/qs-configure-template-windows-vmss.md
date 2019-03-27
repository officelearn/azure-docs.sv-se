---
title: Konfigurera hanterade identiteter för Azure-resurser på en VM-skalningsuppsättning med en mall
description: Stegvisa instruktioner för att konfigurera hanterade identiteter för Azure-resurser på en virtuell datorskalning ange med en Azure Resource Manager-mall.
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
ms.openlocfilehash: 15b37c4845526227799173b09f468701954fc7b5
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449344"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Konfigurera hanterade identiteter för Azure-resurser på en Azure VM-skalningsuppsättning med en mall

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln du lär dig hur du utför följande hanterade identiteter för Azure-resurser åtgärder på en Azure VM-skalningsuppsättning med Azure Resource Manager-Distributionsmall:
- Aktivera och inaktivera systemtilldelade hanterad identitet på en Azure VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad hanterad identitet på en Azure VM-skalningsuppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en hanterad identitet systemtilldelade och användartilldelade](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande Azure rollbaserad åtkomstkontroll tilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:

    > [!NOTE]
    > Inga ytterligare Azure AD directory rolltilldelningar krävs.

    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en skalningsuppsättning för virtuell dator och aktivera och ta bort system och/eller användartilldelade hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad hanterad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad hanterad identitet från och till en VM-skalningsuppsättning.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar tillhandahåller möjligheten att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), eller från den [aktuell status för distributionen](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. Aktivera hanterade identiteter för Azure-resurser på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](../../azure-resource-manager/deployment-modes.md) till distributioner.

## <a name="system-assigned-managed-identity"></a>Systemtilldelade hanterad identitet

I det här avsnittet ska du aktivera och inaktivera systemtilldelade hanterade identiteten med en Azure Resource Manager-mall.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade hanterad identitet när du skapar skapandet av en skalningsuppsättning för virtuella datorer eller en befintlig skalningsuppsättning för virtuella datorer

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.
2. Om du vill aktivera systemtilldelade hanterad identitet, läsa in mallen till en textredigerare, leta upp den `Microsoft.Compute/virtualMachinesScaleSets` resurs i närheten med resurserna och lägger till den `identity` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachinesScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Du kan etablera hanterade identiteter för Azure-resurser VM-skalningsuppsättningen tillägget genom att ange den i den `extensionProfile` elementet i mallen. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.  Mer information finns i [migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).


4. När du är klar bör läggas till resurs i mallen i följande avsnitt och bör likna följande:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en automatiskt genererad hanterad identitet från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en automatiskt genererad hanterad identitet:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachineScaleSets` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som har bara systemtilldelade hanterad identitet kan du inaktivera det genom att ändra identitetstypen till `None`.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**

   Om din apiVersion är `2018-06-01` och den virtuella datorn har både system och användartilldelade hanterade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med userAssignedIdentities ordlista värden.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**

   Om din apiVersion är `2017-12-01` och din skalningsuppsättning för virtuell dator har både system och användartilldelade hanterade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med den `identityIds` matris med de användartilldelade hanteras identiteter. 
   
    

   I följande exempel visas hur bort en automatiskt genererad hanterad identitet från en VM-skalningsuppsättning med inga hanterade användartilldelade identiteter:
   
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

I det här avsnittet tilldelar du en hanterad Användartilldelad identitet till en VM-skalningsuppsättning med Azure Resource Manager-mall.

> [!Note]
> För att skapa en Användartilldelad hanterad identitet med en Azure Resource Manager-mall, se [skapa en hanterad Användartilldelad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Tilldela en hanterad Användartilldelad identitet till en VM-skalningsuppsättning

1. Under den `resources` element, Lägg till följande post för att tilldela en hanterad Användartilldelad identitet till din skalningsuppsättning för virtuell dator.  Se till att ersätta `<USERASSIGNEDIDENTITY>` hanteras med namnet på den Användartilldelad identitet som du skapade.
   
   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**

   Om din apiVersion är `2018-06-01`, dina hanterade användartilldelade identiteter lagras i den `userAssignedIdentities` ordlista format och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i den `variables` i mallen.

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

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2017-12-01**
    
   Om din `apiVersion` är `2017-12-01` eller tidigare, dina hanterade användartilldelade identiteter lagras i den `identityIds` matris och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i avsnittet variabler i mallen.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 
> [!NOTE]
> Du kan etablera hanterade identiteter för Azure-resurser VM-skalningsuppsättningen tillägget genom att ange den i den `extensionProfile` elementet i mallen. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt.  Mer information finns i [migrera från VM-tillägg till Azure IMDS för autentisering](howto-migrate-vm-extension.md).

3. När du är klar bör se din mall ut ungefär så här:
   
   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines API-versionen 2017-12-01**

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
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort Användartilldelad hanterad identitet från en Azure VM-skalningsuppsättning

Om du har en skalningsuppsättning för virtuella datorer som inte längre behöver en hanterad Användartilldelad identitet:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachineScaleSets` resource intressanta inom den `resources` avsnittet. Om du har en skalningsuppsättning för virtuella datorer som endast har användartilldelade hanterad identitet kan du inaktivera det genom att ändra identitetstypen till `None`.

   I följande exempel visas hur bort alla användartilldelade hanterade identiteter från en virtuell dator med inga hanterade systemtilldelade identiteter:

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
   
   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**
    
   Om du vill ta bort en enda hanterad användartilldelade-identitet från en VM-skalningsuppsättning, ta bort den från den `userAssignedIdentities` ordlista.

   Om du har en systemtilldelad identitet kan du förvara den på den i den `type` värde den `identity` värde.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2017-12-01**

   Om du vill ta bort en enda hanterad användartilldelade-identitet från en VM-skalningsuppsättning, ta bort den från den `identityIds` matris.

   Om du har en hanterad identitet med systemtilldelade förvara den på den i den `type` värde den `identity` värde.
   
## <a name="next-steps"></a>Nästa steg

- [Hanterade identiteter för Azure-resurser översikt](overview.md).

