---
title: Konfigurera MSI på en virtuell dator i Azure-skala anges med en mall
description: Stegvisa instruktioner för att konfigurera en hanterad tjänst identitet (MSI) på en Azure-VMSS med en Azure Resource Manager-mall.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 64fe217cf3d845e6a09fe67d03648e79e8a4cadd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Konfigurera en VMSS hanterade tjänstidentiteten med hjälp av en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du utför följande åtgärder för hanterade tjänstidentiteten på en Azure-VMSS med hjälp av Azure Resource Manager-mall för distribution i den här artikeln:
- Aktivera och inaktivera tilldelats identiteten på ett Azure-VMSS
- Lägga till och ta bort en användare som tilldelats identiteten på ett Azure-VMSS

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Som i Azure ge portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller baseras på en befintlig gemensamma eller [QuickStart mallen](https://azure.microsoft.com/documentation/templates/).
   - Härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen av](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md), och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är det under första distributionen och Omdistributionen i mallens syntax. Aktivera MSI på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard, Azure Resource Manager har en [stegvis uppdatering](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) för distributioner.

## <a name="system-assigned-identity"></a>Tilldelats identitet

I det här avsnittet ska du aktivera och inaktivera tilldelats identitet med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Aktivera system för tilldelade identitet under skapandet av en Azure-VMSS eller en befintlig Azure-VMSS

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Efter att läsa in mallen i en textredigerare, leta upp den `Microsoft.Compute/virtualMachineScaleSets` resurs av intresse inom den `resources` avsnitt. Din kan skilja sig något från följande skärmbild, beroende på hur du använder redigeraren och om du redigerar en mall för en ny distribution eller befintlig.
   
   ![Skärmbild av mall - hitta VM](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Om du vill aktivera systemidentitet tilldelade lägger du till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachineScaleSets"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Valfritt) Lägga till virtuella datorns skaluppsättning MSI-tillägg som en `extensionsProfile` element. Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS)-identiteten för att hämta token samt.  Använd följande syntax:

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

   ![Skärmbild av mallen efter uppdateringen](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Inaktivera en tilldelad systemidentitet från en skaluppsättning för virtuell Azure-dator

> [!NOTE]
> Inaktivera hanterade tjänstidentiteten från en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter.

Om du har en virtuell dator skaluppsättningen som inte längre behöver ett system som tilldelats identitet men fortfarande måste användaren som har tilldelats identiteter:

1. Om du loggar in på Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller virtuella datorns skaluppsättning.

2. Ändra identitetstypen till `'UserAssigned'`

## <a name="user-assigned-identity"></a>Användaren som har tilldelats identitet

I det här avsnittet skapar du en användare som tilldelats identitet och en Azure-VMSS med en Azure Resource Manager-mall.

### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vmss"></a>Skapa och tilldela en användare som tilldelats en Azure-VMSS identitet

1. Utför först steget i avsnittet [aktivera tilldelade systemidentitet under skapandet av en Azure-VMSS på en befintlig VMSS](qs-configure-template-windows-vmss.md#enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss).

2. Lägg till en post för en användare tilldelade identitetsnamn liknar följande under avsnittet som innehåller konfigurationsvariabler för Azure-VMSS för variabler.  Det innehåller värdet för den användare som tilldelats identitet under skapandeprocessen Azure VMSS:
    
    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```json
    "variables": {
        "vmssPrefix": "vmss",
        "vmssName": "[concat(variables('vmssPrefix'), uniquestring(resourceGroup().id,deployment().name))]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmssName'), 'id')]"
    ```
3. Under den `resources` element lägger du till följande post för att skapa en tilldelad användaridentitet:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```
4. Sedan under den `resources` element lägger du till följande post att tilldela din VMSS användaridentitet tilldelade:

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/, variables('identityName'))]"
            ]
        }

    }
    ```
5. (Valfritt) Lägg till följande post under den `extensionProfile` element ska tilldelas din VMSS hanterade identity-tillägget. Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt. Använd följande syntax:
   
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
6.  När du är klar bör mallen likna följande:
    > [!NOTE]
    > Mallen innehåller inte alla nödvändiga variabler för att skapa din VMSS.  `//other configuration variables...` används på alla nödvändiga konfigurationsvariabler för enkelhetens planeringsaspekter.

      ![Skärmbild av användaren som har tilldelats identitet](../media/msi-qs-configure-template-windows-vmss/template-vmss-user-assigned-identity.png)

## <a name="next-steps"></a>Nästa steg

- Ett bredare perspektiv om MSI läsa den [hanterade tjänstidentiteten översikt](overview.md).

