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
ms.openlocfilehash: 521c5a3c0ad55afa0b71628195be7782b0e43b67
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Konfigurera en virtuell dator hanteras tjänstidentitet med hjälp av en mall

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade tjänstidentiteten ger Azure-tjänster med en automatiskt hanterade identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering utan autentiseringsuppgifter i koden. 

Lär dig hur du utför följande åtgärder för hanterade tjänstidentiteten på en Azure VM, med hjälp av Azure Resource Manager-mall för distribution i den här artikeln:

## <a name="prerequisites"></a>Förutsättningar

- Om du är bekant med hanterade tjänstidentiteten kan ta en titt på [översiktsavsnittet](overview.md). **Se till att granska den [skillnaden mellan ett system som har tilldelats och användaren som har tilldelats identitet](overview.md#how-does-it-work)**.
- Om du inte redan har ett Azure-konto [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du fortsätter.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Som i Azure ge portal och skript, Azure Resource Manager-mallar möjlighet att distribuera nya eller ändrade resurser som definierats i en Azure-resursgrupp. Flera alternativ är tillgängliga för redigering och distribution, både lokala och portal-baserade, inklusive:

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

I det här avsnittet skapar du en användar-ID och virtuella Azure-datorn med en Azure Resource Manager-mall.

 ### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vm"></a>Skapa och tilldela en användare som tilldelats en Azure VM identitet

1. Utför först steget i avsnittet [aktivera tilldelade systemidentitet vid skapandet av en virtuell dator i Azure eller på en befintlig virtuell dator](#enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm)

2.  Lägg till en post för en användare tilldelade identitetsnamn liknar följande under avsnittet variabler som innehåller konfigurationsvariablerna för din virtuella Azure-datorn.  Detta skapar användaren som har tilldelats identitet under skapandeprocessen virtuella Azure-datorn:
    
    > [!IMPORTANT]
    > Skapa användartilldelade identiteter med specialtecken (dvs understreck) i namnet stöds inte för närvarande. Använd alfanumeriska tecken. Sök igen efter uppdateringar.  Mer information finns i [vanliga frågor och kända problem](known-issues.md)

    ```json
    "variables": {
        "vmName": "[parameters('vmName')]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmName'), 'id')]"
    ```

3. Under den `resources` element, Lägg till följande post för att skapa en tilldelad användaridentitet:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```

4. Sedan under den `resources` element lägger du till följande post att tilldela hanterade identity-tillägg till den virtuella datorn:

    ```json
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
            "type": "ManagedIdentityExtensionForLinux",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
5. Lägg sedan till följande post för att tilldela dina användare som tilldelats identitet till den virtuella datorn:

    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('identityName'))]"
            ]
        },
    ```
6.  När du är klar bör mallen likna följande:
    > [!NOTE]
    > Mallen innehåller inte alla nödvändiga variabler för att skapa den virtuella datorn.  `//other configuration variables...` används på alla nödvändiga konfigurationsvariabler för enkelhetens planeringsaspekter.

      ![Skärmbild av användaren som har tilldelats identitet](../media/msi-qs-configure-template-windows-vm/template-user-assigned-identity.png)


## <a name="related-content"></a>Relaterat innehåll

- Ett bredare perspektiv om MSI läsa den [hanterade tjänstidentiteten översikt](overview.md).

