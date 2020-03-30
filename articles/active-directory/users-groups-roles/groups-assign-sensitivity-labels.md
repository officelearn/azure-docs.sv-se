---
title: Tilldela känslighetsetiketter till grupper – Azure AD | Microsoft-dokument
description: Så här skapar du medlemskapsregler för att automatiskt fylla i grupper och en regelreferens.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/24/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 667fb39aabfec14cff01221b82a45ba8ad1d68d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78329740"
---
# <a name="assign-sensitivity-labels-to-office-365-groups-in-azure-active-directory-preview"></a>Tilldela känslighetsetiketter till Office 365-grupper i Azure Active Directory (förhandsversion)

Azure Active Directory (Azure AD) stöder tillämpning av känslighetsetiketter som publiceras av [Microsoft 365 compliance center](https://sip.protection.office.com/homepage) i Office 365-grupper. Känslighetsetiketter gäller för gruppering mellan tjänster som Outlook, Microsoft Teams och SharePoint. Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. Mer information om stöd för Office 365-appar finns i [Office 365-stöd för känslighetsetiketter](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> För att konfigurera den här funktionen måste det finnas minst en aktiv Azure Active Directory Premium P1-licens i din Azure AD-organisation.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Aktivera stöd för känslighetsetikett i PowerShell

Om du vill använda publicerade etiketter på grupper måste du först aktivera funktionen. De här stegen aktiverar funktionen i Azure AD.

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan förhöjda privilegier.
1. Kör följande kommandon för att förbereda körningen av cmdletarna.

    ```PowerShell
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    På sidan **Logga in på ditt konto** anger du ditt administratörskonto och lösenord för att ansluta dig till din tjänst och väljer Logga **in**.
1. Hämta de aktuella gruppinställningarna för Azure AD-organisationen.

    ```PowerShell
    $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
    ```

    > [!NOTE]
    > Om inga gruppinställningar har skapats för den här Azure AD-organisationen måste du först skapa inställningarna. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-cmdlets) för att skapa gruppinställningar för den här Azure AD-organisationen.

1. Visa sedan de aktuella gruppinställningarna.

    ```PowerShell
    $Setting.Values
    ```

1. Aktivera sedan funktionen:

    ```PowerShell
    $Setting["EnableMIPLabels"] = "True"
    ```

1. Spara sedan ändringarna och tillämpa inställningarna:

    ```PowerShell
    Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
    ```

Klart! Du har aktiverat funktionen och du kan använda publicerade etiketter på grupper.

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Tilldela en etikett till en ny grupp i Azure Portal

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com).
1. Välj **Grupper**och välj sedan **Ny grupp**.
1. På sidan **Ny grupp** väljer du **Office 365**och fyller sedan i den information som krävs för den nya gruppen och väljer en känslighetsetikett i listan.

   ![Tilldela en känslighetsetikett på sidan Nya grupper](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Spara ändringarna och välj **Skapa**.

Gruppen skapas och webbplats- och gruppinställningarna som är associerade med den valda etiketten tillämpas automatiskt.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Tilldela en etikett till en befintlig grupp i Azure Portal

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett administratörskonto för grupper eller som gruppägare.
1. Välj **grupper**.
1. På sidan **Alla grupper** väljer du den grupp som du vill märka.
1. På den valda gruppens sida väljer du **Egenskaper** och väljer en känslighetsetikett i listan.

   ![Tilldela en känslighetsetikett på översiktssidan för en grupp](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Välj **Spara** för att spara ändringarna.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Ta bort en etikett från en befintlig grupp i Azure Portal

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett globalt administratörs- eller gruppadministratörskonto eller som gruppägare.
1. Välj **grupper**.
1. På sidan **Alla grupper** markerar du den grupp som du vill ta bort etiketten från.
1. Välj **Egenskaper**på sidan **Grupp** .
1. Välj **Ta bort**.
1. Klicka på **Spara** för att tillämpa dina ändringar.

## <a name="using-classic-azure-ad-classifications"></a>Använda klassiska Azure AD-klassificeringar

När du har aktiverat den här funktionen visas endast befintliga grupper och webbplatser för de klassiska klassificeringarna för grupper, och du bör bara använda dem för nya grupper om du skapar grupper i appar som inte stöder känslighetsetiketter. Administratören kan konvertera dem till känslighetsetiketter senare om det behövs. Klassiska klassificeringar är de gamla klassificeringar som du `ClassificationList` har ställt in genom att definiera värden för inställningen i Azure AD PowerShell. När den här funktionen är aktiverad tillämpas inte dessa klassificeringar på grupper.

## <a name="troubleshooting-issues"></a>Felsökning av problem

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Känslighetsetiketter är inte tillgängliga för tilldelning i en grupp

Alternativet känslighetsetikett visas endast för grupper när alla följande villkor är uppfyllda:

1. Etiketter publiceras i Microsoft 365 Compliance Center för den här klienten.
1. Funktionen är aktiverad, EnableMIPLabels är inställt på True i PowerShell.
1. Gruppen är en Office 365-grupp.
1. Klienten har en aktiv Azure Active Directory Premium P1-licens.
1. Den aktuella inloggade användaren har tillräcklig behörighet för att tilldela etiketter. Användaren måste vara antingen global administratör, gruppadministratör eller gruppägare.

Kontrollera att alla villkor är uppfyllda för att tilldela etiketter till en grupp.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Etiketten som jag vill tilldela finns inte i listan

Om etiketten du letar efter inte finns med i listan kan det bero på något av följande:

- Etiketten kanske inte publiceras i Microsoft 365 Compliance Center. Detta kan också gälla etiketter som inte längre publiceras. Kontakta administratören för mer information.
- Etiketten kan publiceras, men den är inte tillgänglig för användaren som är inloggad. Kontakta administratören för mer information om hur du får tillgång till etiketten.

### <a name="how-to-change-the-label-on-a-group"></a>Så här ändrar du etiketten på en grupp

Etiketter kan bytas när som helst med samma steg som att tilldela en etikett till en befintlig grupp, enligt följande:

1. Logga in på [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett globalt eller gruppadministratörskonto eller som gruppägare.
1. Välj **grupper**.
1. På sidan **Alla grupper** väljer du den grupp som du vill märka.
1. På den valda gruppens sida väljer du **Egenskaper** och väljer en ny känslighetsetikett i listan.
1. Välj **Spara**.

### <a name="group-setting-changes-to-published-labels-are-not-updated-on-the-groups"></a>Gruppinställningsändringar till publicerade etiketter uppdateras inte i grupperna

Vi rekommenderar inte att du ändrar gruppinställningar för en etikett när etiketten har tillämpats på grupper. När du gör ändringar i gruppinställningar som är associerade med publicerade etiketter i [Microsoft 365 compliance center](https://sip.protection.office.com/homepage)tillämpas dessa principändringar inte automatiskt på de påverkade grupperna.

Om du måste göra en ändring använder du ett [Azure AD PowerShell-skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1) för att manuellt installera uppdateringar för de påverkade grupperna. Den här metoden säkerställer att alla befintliga grupper tillämpar den nya inställningen.

## <a name="next-steps"></a>Nästa steg

- [Använda känslighetsetiketter med Microsoft Teams, Office 365-grupper och SharePoint-webbplatser](https://docs.microsoft.com/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Uppdatera grupper efter ändring av etikettprincip manuellt med Azure AD PowerShell-skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Redigera dina gruppinställningar](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal)
- [Hantera grupper med PowerShell-kommandon](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-settings-v2-cmdlets)
