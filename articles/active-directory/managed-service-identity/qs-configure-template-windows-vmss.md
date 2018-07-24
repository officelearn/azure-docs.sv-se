---
title: Konfigurera MSI för en Azure VM-skalningsuppsättning med en mall
description: Stegvisa instruktioner för att konfigurera en hanterad tjänstidentitet (MSI) på en Azure-VMSS, med en Azure Resource Manager-mall.
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
ms.openlocfilehash: babeb0eb930d865cf519ddb45c651a3d77265665
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215802"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Konfigurera en VMSS hanterad tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterad tjänstidentitet ger Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du utför följande åtgärder för hanterad tjänstidentitet i en Azure-VMSS, med hjälp av Azure Resource Manager-mall för distribution:
- Aktivera och inaktivera systemtilldelad identitet i en Azure VMSS
- Lägga till och ta bort en Användartilldelad identitet i en Azure VMSS

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

Oavsett vilket alternativ som väljs, är densamma under den första distributionen och omdistribution i mallens syntax. När du aktiverar MSI på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard Azure Resource Manager har en [inkrementell uppdatering](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) till distributioner.

## <a name="system-assigned-identity"></a>Systemtilldelad identitet

I det här avsnittet ska du aktivera och inaktivera systemtilldelad identitet med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Aktivera systemtilldelad identitet under skapandet av en Azure-VMSS eller en befintlig Azure VMSS

1. Läsa in mallen till en textredigerare, leta upp den `Microsoft.Compute/virtualMachineScaleSets` resource intressanta inom den `resources` avsnittet. Själv kan skilja sig från följande skärmbild, beroende på redigerare som du använder och om du redigerar en mall för en ny distribution eller befintlig.
   
   ![Skärmbild av mall - Leta upp virtuell dator](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. Om du vill aktivera systemtilldelade identiteter, lägger du till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachineScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Valfritt) Lägga till VM-skalningsuppsättningen MSI-tillägget som en `extensionsProfile` element. Det här steget är valfritt eftersom du kan använda Azure Instance Metadata Service (IMDS)-identiteten för att hämta token samt.  Använd följande syntax:

   >[!NOTE] 
   > I följande exempel förutsätter att en Windows VM scale Sets tillägget (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
   >

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

4. När du är klar bör se din mall ut ungefär så här:

   ![Skärmbild av mall efter uppdateringen](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en systemtilldelade identiteter från en Azure VM-skalningsuppsättning

> [!NOTE]
> Inaktiverar hanterad tjänstidentitet från en virtuell dator stöds för närvarande inte. Under tiden kan växla du mellan att använda System tilldelas och tilldelade användaridentiteter.

Om du har en VM-skalningsuppsättning som inte längre behöver en systemtilldelad identitet men fortfarande ha användartilldelade identiteter:

- Läsa in mallen till en textredigerare och ändra identitetstypen till `'UserAssigned'`

## <a name="user-assigned-identity"></a>Användartilldelad identitet

I det här avsnittet ska tilldela du en Användartilldelad identitet till en Azure-VMSS med hjälp av Azure Resource Manager-mall.

> [!Note]
> För att skapa en Användartilldelad identitet med en Azure Resource Manager-mall, se [skapar du en Användartilldelad identitet](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Tilldela Användartilldelad identitet till en Azure VMSS

1. Under den `resources` element, Lägg till följande post för att tilldela en Användartilldelad identitet till din VMSS.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på Användartilldelad identitet som du skapade.

   > [!Important]
   > Den `<USERASSIGNEDIDENTITYNAME>` värdet som visas i följande exempel måste lagras i en variabel.  Dessutom måste stöds för närvarande implementeringen av tilldelas en virtuell dator i en Resource Manager-mall användartilldelade identiteter, api-versionen överens med versionen i följande exempel. 

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
3.  När du är klar bör se din mall ut ungefär så här:
   
      ![Skärmbild av Användartilldelad identitet](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>Nästa steg

- Ett bredare perspektiv om MSI läsa den [hanterad tjänstidentitet översikt](overview.md).

