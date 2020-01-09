---
title: Konfigurera hur slutanvändare godkänner program med hjälp av Azure AD
description: Lär dig hur du hanterar och när användare kan godkänna program som kommer att ha åtkomst till din organisations data.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443397"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurera hur slutanvändare godkänner program

Program kan integreras med Microsoft Identity Platform så att användarna kan logga in med sitt arbets-eller skol konto i Azure Active Directory (Azure AD) och få åtkomst till din organisations data för att leverera omfattande data drivna upplevelser. Olika behörigheter tillåter programmet olika åtkomst nivåer till dina användare och din organisations data.

Som standard kan användare godkänna program som har åtkomst till din organisations data, men endast för vissa behörigheter. Som standard kan en användare till exempel godkänna att en app får åtkomst till sin egen post låda eller team-konversationer för ett team som användaren äger, men inte kan godkänna att en app utan obevakad åtkomst kan läsa och skriva till alla SharePoint-webbplatser i din organisation. Samtidigt som användare kan godkänna själva kan användarna enkelt förvärva användbara program som integrerar med Microsoft 365, Azure och andra tjänster, men det kan representera en risk om den inte används och övervakas noggrant.

Microsoft rekommenderar att du inaktiverar framtida användar medgivande åtgärder för att minska din yta och minimera risken. Om användar medgivande är inaktiverat kommer tidigare medgivande bidrag fortfarande att bevaras, men alla framtida medgivande åtgärder måste utföras av en administratör. Administratörs medgivande för hela klienten kan begäras av användare via ett integrerat [administrativt medgivande arbets flöde](configure-admin-consent-workflow.md) eller genom egna support processer. Se [fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md) för mer information.

## <a name="configure-user-consent-to-applications"></a>Konfigurera användar medgivande till program
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Inaktivera eller aktivera användar medgivande från Azure Portal

Du kan använda Azure Portal för att inaktivera eller aktivera användarnas möjlighet att godkänna program som har åtkomst till din organisations data:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Välj **Azure Active Directory**, sedan **företags program**och sedan **användar inställningar**.
3. Aktivera eller inaktivera användar medgivande med kontrollen märkta **användare kan godkänna appar som har åtkomst till företags information för deras räkning**.
4. Valfritt Konfigurera [arbets flödet för administratörs godkännande av begäran](configure-admin-consent-workflow.md) för att se till att användare som inte har tillåtelse att godkänna en app kan begära godkännande.

> [!TIP]
> Om du vill tillåta att användare begär en administratörs granskning av ett program som användaren inte har tillåtelse att godkänna till (till exempel eftersom användar medgivande har inaktiverats eller om programmet begär behörigheter som användaren inte har tillåtelse att bevilja) bör du överväga att [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Inaktivera eller aktivera användar medgivande med PowerShell

Du kan använda Azure AD PowerShell v1-modulen ([MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) för att aktivera eller inaktivera användarnas möjlighet att godkänna program som har åtkomst till organisationens data.

1. Logga in på din organisation genom att köra den här cmdleten:

    ```powershell
    Connect-MsolService
    ```

2. Kontrol lera om användar medgivande har Aktiver ATS genom att köra denna cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Aktivera eller inaktivera användar medgivande. Om du till exempel vill inaktivera användar medgivande kör du denna cmdlet:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurera grupp ägare medgivande till appar som har åtkomst till grupp data

> [!IMPORTANT]
> Följande information gäller för en kommande funktion som gör det möjligt för grupp ägare att ge program åtkomst till sina gruppers data. När den här funktionen släpps, aktive ras den som standard. Även om den här funktionen ännu inte har släppts mycket, kan du använda dessa instruktioner för att inaktivera funktionen i förväg.

Grupp ägare kan auktorisera program (till exempel program som publicerats av tredjepartsleverantörer) för att komma åt din organisations data som är associerade med en grupp. Till exempel kan en grupp ägare (som är ägare av Office 365-gruppen för teamet) tillåta att en app läser alla team meddelanden i teamet eller visar den grundläggande profilen för en grupps medlemmar.

> [!NOTE]
> Oberoende av den här inställningen tillåts en grupp ägare alltid att lägga till andra användare eller appar direkt som grupp ägare.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurera grupp ägarens medgivande med PowerShell

Du kan använda Azure AD PowerShell Preview-modulen ([AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)) för att aktivera eller inaktivera grupp ägarens möjlighet att godkänna program som har åtkomst till organisationens data för de grupper som de äger.

1. Se till att du använder modulen [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (det här steget är viktigt om du har installerat både [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) -modulen och [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) -modulen).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Hämta det aktuella värdet för katalog inställningarna för *medgivande princip inställningarna* i din klient organisation. Detta kräver att du kontrollerar om katalog inställningarna för den här funktionen har skapats, och om inte använder värdena från motsvarande katalog inställnings mall.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. Förstå inställnings värden. Det finns två inställnings värden som definierar vilka användare som ska kunna tillåta en app åtkomst till deras grupp data:

    | Inställning       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolean |  Flagga som anger om grupp ägare tillåts att bevilja gruppdefinierade behörigheter. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Om _EnableGroupSpecificConsent_ är inställt på "true" och det här värdet har angetts till en grupps objekt-ID, kommer medlemmar i gruppen som identifieras att kunna bevilja gruppspecifika behörigheter till de grupper som de äger. |

5. Uppdatera inställnings värden för önskad konfiguration:

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. Spara inställningar.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>Nästa steg

[Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)

[Bevilja ett program administratörs medgivande för hela klienten](grant-admin-consent.md)

[Behörigheter och medgivande i Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
