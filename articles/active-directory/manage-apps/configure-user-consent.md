---
title: Konfigurera hur slutanvändare samtycker till program som använder Azure AD
description: Läs om hur du hanterar hur och när användare kan samtycka till program som har åtkomst till organisationens data.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443397"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurera hur slutanvändare samtycker till program

Program kan integreras med Microsoft Identity-plattformen så att användare kan logga in med sitt arbets- eller skolkonto i Azure Active Directory (Azure AD) och för att komma åt organisationens data för att leverera omfattande datadrivna upplevelser. Olika behörigheter ger programmet olika åtkomstnivå till användarnas och organisationens data.

Som standard kan användare samtycka till program som använder organisationens data, men bara för vissa behörigheter. Som standard kan en användare till exempel samtycka till att tillåta att en app får åtkomst till sin egen postlåda eller Teams-konversationerna för ett team som användaren äger, men kan inte samtycka till att tillåta att en app har obevakad åtkomst för att läsa och skriva till alla SharePoint-webbplatser i organisationen. Samtidigt som användare kan samtycka själva tillåter användare att enkelt skaffa användbara program som integreras med Microsoft 365, Azure och andra tjänster, kan det utgöra en risk om den inte används och övervakas noggrant.

Microsoft rekommenderar att du inaktiverar framtida åtgärder för användargodkännande för att minska din yta och minska den här risken. Om användarens medgivande inaktiveras kommer tidigare medgivandebidrag fortfarande att respekteras, men alla framtida medgivandeåtgärder måste utföras av en administratör. Administratörsmedgivande för hela klienten kan begäras av användare via ett arbetsflöde för en integrerad [administratörsföryndebegäran](configure-admin-consent-workflow.md) eller genom dina egna supportprocesser. Mer information finns i Fem steg för [att skydda din identitetsinfrastruktur.](../../security/fundamentals/steps-secure-identity.md)

## <a name="configure-user-consent-to-applications"></a>Konfigurera användarens medgivande till program
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Inaktivera eller aktivera användarmedgivande från Azure-portalen

Du kan använda Azure-portalen för att inaktivera eller aktivera användarnas möjlighet att godkänna program som använder organisationens data:

1. Logga in på [Azure-portalen](https://portal.azure.com) som [global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Välj **Azure Active Directory**och sedan **Företagsprogram**och sedan **Användarinställningar**.
3. Aktivera eller inaktivera användarens medgivande med kontrollen som heter **Användare kan samtycka till att appar som får åtkomst till företagsdata för deras räkning**.
4. (Valfritt) Konfigurera [arbetsflödet för administratörsförsyrkan för](configure-admin-consent-workflow.md) att säkerställa att användare som inte har tillstånd att samtycka till en app kan begära godkännande.

> [!TIP]
> Om du vill tillåta användare att begära en administratörs granskning av ett program som användaren inte får samtycka till (till exempel på grund av att användarens medgivande har inaktiverats eller på grund av att programmet begär behörigheter som användaren inte får bevilja) bör du överväga att konfigurera arbetsflödet för [administratörssamtycke](configure-admin-consent-workflow.md).

### <a name="disable-or-enable-user-consent-using-powershell"></a>Inaktivera eller aktivera användarens medgivande med PowerShell

Du kan använda Azure AD PowerShell v1-modulen[(MSOnline](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)) för att aktivera eller inaktivera användarnas möjlighet att samtycka till program som använder organisationens data.

1. Logga in på din organisation genom att köra den här cmdlet:

    ```powershell
    Connect-MsolService
    ```

2. Kontrollera om användarens medgivande är aktiverat genom att köra den här cmdlet:

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. Aktivera eller inaktivera användarens medgivande. Om du till exempel vill inaktivera användarens medgivande kör du den här cmdleten:

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurera gruppägares medgivande till appar som använder gruppdata

> [!IMPORTANT]
> Följande information är för en kommande funktion som gör det möjligt för gruppägare att ge program åtkomst till sina gruppers data. När den här funktionen släpps aktiveras den som standard. Även om den här funktionen ännu inte har släppts i stor utsträckning kan du använda dessa instruktioner för att inaktivera funktionen före dess release.

Gruppägare kan auktorisera program (till exempel program som publiceras av tredjepartsleverantörer) för att komma åt organisationens data som är associerade med en grupp. En gruppägare (som är ägare till Office 365-gruppen för teamet) kan till exempel tillåta en app att läsa alla Teams-meddelanden i teamet eller lista den grundläggande profilen för en grupps medlemmar.

> [!NOTE]
> Oberoende av den här inställningen kan en gruppägare alltid lägga till andra användare eller appar direkt som gruppägare.

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurera gruppägare med hjälp av PowerShell

Du kan använda Azure AD PowerShell Preview-modulen[(AzureADPreview)](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)för att aktivera eller inaktivera gruppägares möjlighet att godkänna program som använder organisationens data för de grupper de äger.

1. Se till att du använder [Modulen AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) (det här steget är viktigt om du har installerat både [AzureAD-modulen](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) och [AzureADPreview-modulen).](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

3. Hämta det aktuella värdet för kataloginställningarna för inställningar för *consent-princip* i klienten. Detta kräver kontroll av om kataloginställningarna för den här funktionen har skapats, och om inte, med hjälp av värdena från motsvarande kataloginställningar mall.

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

4. Förstå inställningsvärdena. Det finns två inställningsvärden som definierar vilka användare som skulle kunna tillåta en app att komma åt gruppens data:

    | Inställning       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _AktiveraGroupSpecificConsent_   | Boolean |  Flagga som anger om gruppägare tillåts bevilja gruppspecifika behörigheter. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Om _EnableGroupSpecificConsent_ är inställt på "True" och det här värdet anges till en grupps objekt-ID, kommer medlemmar i den identifierade gruppen att ha behörighet att bevilja gruppspecifika behörigheter till de grupper de äger. |

5. Uppdatera inställningsvärden för önskad konfiguration:

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

[Konfigurera arbetsflödet för administratörsgodkännande](configure-admin-consent-workflow.md)

[Bevilja administratör för hela klienten till ett program](grant-admin-consent.md)

[Behörigheter och medgivande på Microsofts identitetsplattform](../develop/active-directory-v2-scopes.md)

[Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
