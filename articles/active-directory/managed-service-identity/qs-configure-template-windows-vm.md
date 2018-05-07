---
title: Hur du konfigurerar MSI på en virtuell dator i Azure med hjälp av en mall
description: Stegvisa instruktioner för att konfigurera en hanterad tjänst identitet (MSI) på en Azure-dator med en Azure Resource Manager-mall.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 324a1e08e92a2c7ae76d7a6df56536540dc772a1
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/04/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du utför följande åtgärder för hanterade tjänstidentiteten på en Azure VM, med hjälp av Azure Resource Manager-mall för distribution i den här artikeln:

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Precis som med Azure portal och skript, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) mallar tillhandahåller möjligheten att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

   - Med hjälp av en [anpassad mall från Azure Marketplace](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), där du kan skapa en mall från början eller baseras på en befintlig gemensamma eller [QuickStart mallen](https://azure.microsoft.com/documentation/templates/).
   - Härleds från en befintlig resursgrupp genom att exportera en mall från antingen [den ursprungliga distributionen av](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), eller från den [aktuell status för distributionen](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Använda en lokal [JSON-redigerare (till exempel VS-kod)](../../azure-resource-manager/resource-manager-create-first-template.md), och sedan ladda upp och distribuera med hjälp av PowerShell eller CLI.
   - Med Visual Studio [Azure-resursgruppsprojekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) både skapa och distribuera en mall.  

Oavsett vilket alternativ du väljer, är det under första distributionen och Omdistributionen i mallens syntax. Aktivera system eller användaren som har tilldelats identiteten på en ny eller befintlig virtuell dator görs på samma sätt. Dessutom som standard, Azure Resource Manager har en [stegvis uppdatering](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) för distributioner.

## <a name="system-assigned-identity"></a>Tilldelats identitet

I det här avsnittet ska du aktivera och inaktivera ett system som tilldelats identitet med hjälp av en Azure Resource Manager-mall.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Aktivera system för tilldelade identitet vid skapandet av en virtuell dator i Azure eller på en befintlig virtuell dator

1. Om du loggar in till Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel roll ”Virtual Machine-deltagare”).

2. Efter att läsa in mallen i en textredigerare, leta upp den `Microsoft.Compute/virtualMachines` resurs av intresse inom den `resources` avsnitt. Din kan skilja sig något från följande skärmbild, beroende på hur du använder redigeraren och om du redigerar en mall för en ny distribution eller befintlig.

   >[!NOTE] 
   > Det här exemplet förutsätter variabler som `vmName`, `storageAccountName`, och `nicName` har definierats i mallen.
   >

   ![Skärmbild av mall - hitta VM](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Aktivera tilldelats identitet genom att lägga till den `"identity"` egenskapen på samma nivå som den `"type": "Microsoft.Compute/virtualMachines"` egenskapen. Använd följande syntax:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Valfritt) Lägg till VM MSI-tillägget som en `resources` element. Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt.  Använd följande syntax:

   >[!NOTE] 
   > Följande exempel förutsätter en Windows VM-tillägget (`ManagedIdentityExtensionForWindows`) som ska distribueras. Du kan också konfigurera för Linux med hjälp av `ManagedIdentityExtensionForLinux` i stället för den `"name"` och `"type"` element.
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

5. När du är klar bör mallen likna följande:

   ![Skärmbild av mallen efter uppdateringen](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Inaktivera ett system som tilldelats identitet från en Azure VM

> [!NOTE]
> Inaktivera hanterade tjänstidentiteten från en virtuell dator stöds inte för närvarande. Under tiden kan växla du mellan att använda System tilldelade och tilldelade användaridentiteter.

Om du har en virtuell dator som inte längre behöver en hanterade tjänstidentiteten:

1. Om du loggar in till Azure lokalt eller via Azure portal, kan du använda ett konto som är associerade med Azure-prenumeration som innehåller den virtuella datorn. Se också till att ditt konto tillhör en roll som ger dig skrivbehörighet på den virtuella datorn (till exempel roll ”Virtual Machine-deltagare”).

2. Ändra identitetstypen till `UserAssigned`.

## <a name="user-assigned-identity"></a>Användaren som har tilldelats identitet

I det här avsnittet tilldelar du en identitet för användaren som har tilldelats till en virtuell dator i Azure med hjälp av Azure Resource Manager-mall.

> [!Note]
> Om du vill skapa en identitet för användaren som har tilldelats med hjälp av en Azure Resource Manager-mall finns [skapa en identitet för användaren som har tilldelats](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Tilldela en användare som tilldelats en Azure VM identitet

1. Under den `resources` element, Lägg till följande post för att tilldela en identitet för användaren som har tilldelats till den virtuella datorn.  Se till att ersätta `<USERASSIGNEDIDENTITY>` med namnet på användaren som har tilldelats identiteten som du skapade.
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITYNAME>)']"
            ]
        },
    ```
    
2. (Valfritt) Sedan under den `resources` element, Lägg till följande post för att tilldela hanterade identity-tillägget till den virtuella datorn. Det här steget är valfritt eftersom du kan använda Azure instans Metadata Service (IMDS) identitet slutpunkt, för att hämta token samt. Använd följande syntax:
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
    
3.  När du är klar bör mallen likna följande:

      ![Skärmbild av användaren som har tilldelats identitet](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Relaterat innehåll

- Ett bredare perspektiv om MSI läsa den [hanterade tjänstidentiteten översikt](overview.md).

