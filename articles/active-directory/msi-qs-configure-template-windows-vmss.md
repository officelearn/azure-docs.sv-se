---
title: "Konfigurera MSI på en virtuell dator i Azure-skala anges med en mall"
description: "Stegvisa instruktioner för att konfigurera en hanterad tjänst identitet (MSI) på en Azure-VMSS med en Azure Resource Manager-mall."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 669348213e00cb338c69156f37779767c681a227
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Hanterad Service identitet (MSI) ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

I den här artikeln du lära dig hur du aktiverar och ta bort MSI för en skaluppsättning för virtuell Azure-dator med en Distributionsmall av Azure Resource Manager.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Aktivera MSI under skapandet av en skaluppsättning för virtuell Azure-dator eller en befintlig skaluppsättning för virtuell Azure-dator

Som i Azure ge portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller baseras på en befintlig gemensamma eller [QuickStart mallen](https://azure.microsoft.com/documentation/templates/).
   - Härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen av](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../azure-resource-manager/resource-manager-create-first-template.md), och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är det under första distributionen och Omdistributionen i mallens syntax. Aktiverar MSI på en ny eller befintlig Azure virtuella datorns skaluppsättning görs på samma sätt. Dessutom som standard, Azure Resource Manager har en [stegvis uppdatering](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) för distributioner:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Efter att läsa in mallen i en textredigerare, leta upp den `Microsoft.Compute/virtualMachineScaleSets` resurs av intresse inom den `resources` avsnitt. Din kan skilja sig något från följande skärmbild, beroende på hur du använder redigeraren och om du redigerar en mall för en ny distribution eller befintlig.
   
   ![Skärmbild av mall - hitta VM](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Lägg till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachineScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Lägg sedan till virtuella datorns skaluppsättning ange MSI-tillägg som en `extensionsProfile` element. Använd följande syntax:

   >[!NOTE] 
   > Följande exempel förutsätter att en virtuell Windows-skala ange tillägg (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
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

5. När du är klar bör mallen likna följande:

   ![Skärmbild av mallen efter uppdateringen](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Ta bort MSI från en skaluppsättning för virtuell Azure-dator

Om du har en skaluppsättning för virtuell dator som inte längre behöver en MSI:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Ta bort de två element som har lagts till i föregående avsnitt: virtuella datorns skaluppsättning `"identity"` och `"extensionsProfile"` egenskaper.

## <a name="next-steps"></a>Nästa steg

- Ett bredare perspektiv om MSI läsa den [hanterade tjänstidentiteten översikt](msi-overview.md).

