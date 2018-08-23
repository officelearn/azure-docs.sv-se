---
title: Konfigurera hanterad tjänstidentitet på en Azure VM-skalningsuppsättning med en mall
description: Stegvisa instruktioner för att konfigurera en hanterad tjänstidentitet på en Azure-VMSS, med en Azure Resource Manager-mall.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 68304b3e5eea50aba28f46344abcbd7ad060c5c8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059581"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Konfigurera hanterad tjänstidentitet på VM-skalningsuppsättning med en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterad tjänstidentitet för Azure VM-skalningsuppsättning, med hjälp av mall för distribution av Azure Resource Manager:
- Aktivera och inaktivera den systemtilldelade identiteten på en Azure VM-skalningsuppsättning
- Lägga till och ta bort en Användartilldelad identitet på en Azure VM-skalningsuppsättning

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterad tjänstidentitet kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan en systemtilldelad och Användartilldelad identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrerar du dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.
- Ditt konto måste följande rolltilldelningar för att utföra vilka hanteringsåtgärder i den här artikeln:
    - [Virtuell Datordeltagare](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) att skapa en virtuell datorskalning och aktivera och ta bort system och/eller användare tilldelas hanterad identitet från en skalningsuppsättning för virtuell dator.
    - [Hanterad Identitetsdeltagare](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) roll för att skapa en Användartilldelad identitet.
    - [Hanterade Identitetsoperatör](/azure/role-based-access-control/built-in-roles#managed-identity-operator) roll att tilldela och ta bort en Användartilldelad identitet från och till en VM-skalningsuppsättning.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure-portalen och skript, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar tillhandahåller möjligheten att distribuera nya eller ändrade resurser som definierats av en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portalbaserad, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller basera den på en befintlig common eller [snabbstartsmall](https://azure.microsoft.com/documentation/templates/).
   - Som härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Med hjälp av en lokal [JSON-redigerare (till exempel VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), överföring och distribution med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) att både skapa och distribuera en mall.  

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. Aktiverar hanterad tjänstidentitet på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](../../azure-resource-manager/deployment-modes.md) till distributioner.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet ska du aktivera och inaktivera systemtilldelad identitet med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Aktivera systemtilldelade identiteter under skapandet skapandet av en skalningsuppsättning för virtuella datorer eller en befintlig skalningsuppsättning för virtuella datorer

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.
   
2. Att aktivera systemtilldelade identiteter, läsa in mallen till en textredigerare, leta upp den `Microsoft.Compute/virtualMachinesScaleSets` resurs i närheten med resurserna och lägger till den `identity` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Valfritt) Lägga till VM-skalningsuppsättningen hanterad tjänstidentitet tillägg som en `extensionsProfile` element. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS)-identiteten för att hämta token samt.  Använd följande syntax:

   >[!NOTE] 
   > I följande exempel förutsätter att en Windows VM scale Sets tillägget (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en systemtilldelade identiteter från en Azure VM-skalningsuppsättning

Om du har en VM-skalningsuppsättning som inte längre behöver du en hanterad tjänstidentitet:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachineScaleSets` resource intressanta inom den `resources` avsnittet. Om du har en virtuell dator som har bara systemtilldelade identiteter kan du inaktivera det genom att ändra identitetstypen till `None`.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**

   Om din apiVersion är `2018-06-01` och den virtuella datorn har både system och användartilldelade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med userAssignedIdentities ordlista värden.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01 och tidigare**

   Om din apiVersion är `2017-12-01` och din skalningsuppsättning för virtuell dator har både system och användartilldelade identiteter, ta bort `SystemAssigned` från identitetstyp och håll `UserAssigned` tillsammans med den `identityIds` matris med användartilldelade identiteter. 
   
    

   I följande exempel visas hur bort systemtilldelad identitet från en VM-skalningsuppsättning med inga användartilldelade identiteter:
   
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

## <a name="user-assigned-identity"></a>Användartilldelad identitet

I det här avsnittet ska tilldela du Användartilldelad identitet till en VM-skalningsuppsättning med Azure Resource Manager-mall.

> [!Note]
> För att skapa en Användartilldelad identitet med en Azure Resource Manager-mall, se [skapar du en Användartilldelad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Tilldela Användartilldelad identitet till en Azure VMSS

1. Under den `resources` element, Lägg till följande post för att tilldela en Användartilldelad identitet till din skalningsuppsättning för virtuell dator.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på Användartilldelad identitet som du skapade.
   
   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2018-06-01**

   Om din apiVersion är `2018-06-01`, användartilldelade identiteter lagras i den `userAssignedIdentities` ordlista format och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i den `variables` i mallen.

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
    
   Om din `apiVersion` är `2017-12-01` eller tidigare, användartilldelade identiteter lagras i den `identityIds` matris och `<USERASSIGNEDIDENTITYNAME>` värdet måste vara lagrad i en variabel som anges i avsnittet variabler i mallen.

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

2. (Valfritt) Lägg till följande post under den `extensionProfile` elementet ska tilldelas din VMSS tillägget hanterad identitet. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS) identitet slutpunkten, för att hämta token samt. Använd följande syntax:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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

   **Microsoft.Compute/virtualMachines API version 2017-12-01-tidigare eand**

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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Ta bort Användartilldelad identitet från en Azure VM-skalningsuppsättning

Om du har en VM-skalningsuppsättning som inte längre behöver du en hanterad tjänstidentitet:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumerationen som innehåller virtuella datorns skalningsuppsättning.

2. Läsa in mallen till en [redigeraren](#azure-resource-manager-templates) och leta upp den `Microsoft.Compute/virtualMachineScaleSets` resource intressanta inom den `resources` avsnittet. Om du har en skalningsuppsättning för virtuella datorer som endast har Användartilldelad identitet kan du inaktivera det genom att ändra den identitetstypen till `None`.

   I följande exempel visas hur bort alla användartilldelade identiteter från en virtuell dator med inga systemtilldelade identiteter:

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
    
   Ta bort en en enda Användartilldelad identitet från en skalningsuppsättning för virtuella datorer, ta bort den från den `userAssignedIdentities` ordlista.

   Om du har en systemtilldelade identiteter kan du förvara den på den i den `type` värde den `identity` värde.

   **Microsoft.Compute/virtualMachineScaleSets API-versionen 2017-12-01**

   Om du vill ta bort en enskild Användartilldelad identitet från en VM-skalningsuppsättning, ta bort den från den `identityIds` matris.

   Om du har en systemtilldelade identiteter kan du förvara den på den i den `type` värde den `identity` värde.
   
## <a name="next-steps"></a>Nästa steg

- Ett bredare perspektiv om hanterad tjänstidentitet läsa den [hanterad tjänstidentitet översikt](overview.md).

