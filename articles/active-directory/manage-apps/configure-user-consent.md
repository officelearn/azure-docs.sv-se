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
ms.date: 05/19/2020
ms.author: mimart
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0a508e52189938447ea6fc1928d441d81deab392
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714037"
---
# <a name="configure-how-end-users-consent-to-applications"></a>Konfigurera hur slutanvändare godkänner program

Du kan integrera dina program med Microsoft Identity Platform så att användarna kan logga in med sitt arbets-eller skol konto och komma åt din organisations data för att leverera omfattande data drivna upplevelser.

Innan ett program kan komma åt din organisations data måste en användare ge programmet behörighet att göra det. Olika behörigheter tillåter olika åtkomst nivåer. Som standard har alla användare tillåtelse att godkänna program för behörigheter som inte kräver administratörs medgivande. Som standard kan en användare godkänna att en app får åtkomst till sin post låda men inte kan godkänna att en app oinskränkt till att läsa och skriva till alla filer i din organisation.

Genom att tillåta användare att ge appar åtkomst till data kan användarna enkelt få användbara program och vara produktiva. Men i vissa fall kan den här konfigurationen representera en risk om den inte övervakas och kontrol leras noggrant.

## <a name="user-consent-settings"></a>Inställningar för godkännande av användare

Om du vill styra vilka fall användare kan godkänna program väljer du den medgivande princip som ska gälla för alla användare. Här är de tre princip alternativen för medgivande:

* **Inaktivera användar medgivande** – användare kan inte bevilja åtkomst till program. Användare kan fortsätta att logga in på appar som de tidigare har samtyckt till eller som har godkänts av administratörer för deras räkning, men de kommer inte att kunna samtycka till nya behörigheter eller till nya appar. Endast användare som har beviljats en katalog roll som innehåller behörigheten för att bevilja tillstånd kan godkänna nya behörigheter eller nya appar.

* **Användare kan godkänna appar från verifierade utgivare, men endast för behörigheter som du väljer (förhands granskning)** – alla användare kan bara godkänna appar som publicerats av en [verifierad utgivare](../develop/publisher-verification-overview.md) och appar som är registrerade i din klient organisation. Användare kan bara godkänna de behörigheter som du har klassificerat som "låg påverkan".

  Se till att [klassificera behörigheter](#configure-permission-classifications-preview) för att välja vilka behörigheter som användare tillåts att godkänna.

* **Användare kan godkänna alla appar** – det här alternativet gör att alla användare kan godkänna alla behörigheter, vilket inte kräver administratörs medgivande, för alla program. 

   För att minska risken för skadliga program som försöker lura användare att ge dem åtkomst till din organisations data, rekommenderar vi att du endast tillåter användar medgivande för program som har publicerats av en [verifierad utgivare](../develop/publisher-verification-overview.md).

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Konfigurera inställningar för användar medgivande från Azure Portal

Konfigurera användar medgivande inställningar via Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och**  >  **användar**tillstånds inställningar.
1. Under **användar medgivande för program**väljer du vilken godkännande inställning som du vill konfigurera för alla användare.
1. Spara inställningarna genom att välja **Spara** .

![Inställningar för godkännande av användare](./media/configure-user-consent/setting-for-all-users.png)

> [!TIP]
> Överväg att [Aktivera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md) så att användarna kan begära en administratörs granskning och godkännande av ett program som användaren inte får bevilja tillåtelse till, till exempel när användar medgivande har inaktiverats eller när ett program begär behörigheter som användaren inte har tillåtelse att bevilja.

### <a name="configure-user-consent-settings-using-powershell"></a>Konfigurera inställningar för användar godkännande med PowerShell

Du kan använda den senaste Azure AD PowerShell Preview-modulen, [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview), för att välja vilken princip för medgivande som styr användar medgivande för program.

* **Inaktivera användar medgivande** – om du vill inaktivera användar medgivande anger du de medgivande principer som styr användar medgivande som ska vara tomt:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **Tillåt användar medgivande för appar från verifierade utgivare, för valda behörigheter (för hands version)** – för att tillåta begränsat användar medgivande enbart för appar från verifierade utgivare och appar som registrerats i din klient, och endast för behörigheter som du klassificerar som "låg påverkan", konfigurerar du den inbyggda medgivande principen med namnet `microsoft-user-default-low` :

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   Glöm inte att [klassificera behörigheter](#configure-permission-classifications-preview) för att välja vilka behörigheter som användare tillåts att samtycka till.

* **Tillåt användar medgivande för alla appar** – för att tillåta användar medgivande för alla appar:

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   Med det här alternativet kan alla användare godkänna alla behörigheter som inte kräver administratörs medgivande, för alla program. Vi rekommenderar att du endast tillåter användar medgivande för appar från verifierade utgivare.

## <a name="configure-permission-classifications-preview"></a>Konfigurera behörighets klassificeringar (förhands granskning)

Med behörighets klassificeringar kan du identifiera vilken inverkan olika behörigheter har enligt organisationens principer och riskbedömningar. Du kan till exempel använda behörighets klassificeringar i principer för medgivande för att identifiera den uppsättning behörigheter som användare tillåts att samtycka till.

> [!NOTE]
> För närvarande stöds inte behörighets klassificeringen "låg effekt". Endast delegerade behörigheter som inte kräver administratörs medgivande kan klassificeras som "låg påverkan".

### <a name="classify-permissions-using-the-azure-portal"></a>Klassificera behörigheter med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
1. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och behörighets**  >  **klassificering**.
1. Välj **Lägg till behörigheter** för att klassificera en annan behörighet som "låg påverkan". 
1. Välj API och välj sedan de delegerade behörigheterna.

I det här exemplet har vi klassificerat den minsta behörighets uppsättningen som krävs för enkel inloggning:

![Behörighets klassificering](./media/configure-user-consent/permission-classifications.png)

> [!TIP]
> För Microsoft Graph-API: t är de lägsta behörigheter som krävs för grundläggande enkel inloggning `openid` är `profile` , `User.Read` och `offline_access` . Med dessa behörigheter kan en app läsa profil information för den inloggade användaren och upprätthålla den här åtkomsten även om användaren inte längre använder appen.

### <a name="classify-permissions-using-powershell"></a>Klassificera behörigheter med PowerShell

Du kan använda den senaste Azure AD PowerShell Preview-modulen, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), för att klassificera behörigheter. Behörighets klassificeringar konfigureras på **ServicePrincipal** -objektet för API: et som publicerar behörigheterna.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>Så här läser du de aktuella behörighets klassificeringarna för ett API:

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Läs klassificeringarna för delegerad behörighet för API: et:

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>Klassificera en behörighet som "låg påverkan":

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Sök efter den delegerade behörighet som du vill klassificera:

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. Ange behörighets klassificeringen med hjälp av behörighets namnet och ID: t:

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>Ta bort en delegerad behörighets klassificering:

1. Hämta **ServicePrincipal** -objektet för API: et. Här hämtar vi ServicePrincipal-objektet för Microsoft Graph API:

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. Hitta den delegerade behörighets klassificering som du vill ta bort:

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. Ta bort behörighets klassificeringen:

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>Konfigurera grupp ägare medgivande till appar som har åtkomst till grupp data

Grupp ägare kan auktorisera program, till exempel program som publiceras av tredjepartsleverantörer, för att komma åt din organisations data som är associerade med en grupp. Till exempel kan en grupp ägare i Microsoft-team tillåta att en app läser alla team meddelanden i teamet eller listar grund profilen för en grupps medlemmar.

Du kan konfigurera vilka användare som tillåts att godkänna appar som har åtkomst till deras grupp data, eller så kan du inaktivera den här funktionen.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Konfigurera grupp ägarens medgivande med hjälp av Azure Portal

1. Logga in på [Azure Portal](https://portal.azure.com) som [Global administratör](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator).
2. Välj **Azure Active Directory**  >  **företags program**  >  **medgivande-och**  >  **användar**tillstånds inställningar.
3. Under **grupp ägarens medgivande för appar som har åtkomst till data** väljer du det alternativ som du vill aktivera.
4. Spara inställningarna genom att välja **Spara** .

I det här exemplet har alla grupp ägare tillåtelse att tillåta appar som har åtkomst till sina gruppers data:

![Behörighets klassificering](./media/configure-user-consent/group-owner-consent.png)

### <a name="configure-group-owner-consent-using-powershell"></a>Konfigurera grupp ägarens medgivande med PowerShell

Du kan använda Azure AD PowerShell Preview-modulen, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), för att aktivera eller inaktivera grupp ägarens möjlighet att godkänna program som har åtkomst till organisationens data för de grupper som de äger.

1. Kontrol lera att du använder [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) -modulen. Det här steget är viktigt om du har installerat både [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) -modulen och [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) -modulen.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Anslut till Azure AD PowerShell.

   ```powershell
   Connect-AzureAD
   ```

1. Hämta det aktuella värdet för katalog inställningarna för **medgivande princip inställningarna** i din klient organisation. Detta kräver att du kontrollerar om katalog inställningarna för den här funktionen har skapats, och om inte använder värdena från motsvarande katalog inställnings mall.

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

1. Förstå inställnings värden. Det finns två inställnings värden som definierar vilka användare som ska kunna tillåta en app åtkomst till deras grupp data:

    | Inställningen       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | Boolesk | Flagga som anger om grupp ägare tillåts att bevilja gruppdefinierade behörigheter. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | GUID | Om _EnableGroupSpecificConsent_ är inställt på "true" och det här värdet har angetts till en grupps objekt-ID kommer medlemmar i den identifierade gruppen ha behörighet att bevilja gruppspecifika behörigheter till de grupper som de äger. |

1. Uppdatera inställnings värden för önskad konfiguration:

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

1. Spara inställningarna.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>Konfigurera riskfylldt steg-för-steg-godkännande

Riskfylldt steg-för-steg-godkännande bidrar till att minska användar exponeringen för skadliga appar som gör [illegala medgivande begär Anden](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants). Om Microsoft identifierar en riskfylld begäran om godkännande av slutanvändare kräver begäran ett "steg-för-steg" för administratörs medgivande i stället. Den här funktionen är aktive rad som standard, men den kommer bara att resultera i en funktion som ändras när slutanvändaren har Aktiver ATS.

När en riskfylld begäran identifieras visar medgivande frågan ett meddelande som anger att administratörs godkännande krävs. Om [arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md) är aktiverat kan användaren skicka begäran till en administratör för ytterligare granskning direkt från medgivande frågan. Om den inte är aktive rad visas följande meddelande:

* **AADSTS90094:** &lt; clientAppDisplayName &gt; måste ha behörighet för att få åtkomst till resurser i din organisation som bara en administratör kan bevilja. Be en administratör att bevilja behörighet till den här appen innan du använder den.

I det här fallet loggas även en gransknings händelse med en kategori av typen "ApplicationManagement", aktivitets typ "medgivande till program" och status orsak för "riskfylld program upptäcktes".

> [!IMPORTANT]
> Administratörer bör [utvärdera alla medgivande begär Anden](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent) noggrant innan en begäran godkänns, särskilt när Microsoft har identifierat risk.

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>Inaktivera eller återaktivera riskfylldt Step-based-godkännande med PowerShell

Du kan använda Azure AD PowerShell Preview-modulen, [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview), för att inaktivera steget-upp till administratörs medgivande som krävs i de fall där Microsoft identifierar risker eller aktiverar det igen om det tidigare har inaktiverats.

Du kan göra detta med hjälp av samma steg som ovan för att [Konfigurera grupp ägarens medgivande med hjälp av PowerShell](#configure-group-owner-consent-using-powershell), men som ersätter ett annat inställnings värde. Det finns tre skillnader i stegen: 

1. Förstå inställnings värden för riskfylldt steg-för-steg-godkännande:

    | Inställningen       | Typ         | Beskrivning  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | Boolesk |  Flagga som anger om användar medgivande ska blockeras när en riskfylld begäran identifieras. |

1. Ersätt följande värde i steg 3:

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. Ersätt något av följande i steg 5:

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>Nästa steg

Mer information:

* [Konfigurera arbets flödet för administratörs medgivande](configure-admin-consent-workflow.md)
* [Lär dig hur du hanterar medgivande till program och att utvärdera medgivande begär Anden](manage-consent-requests.md)
* [Bevilja administratörsmedgivande för hela klientorganisationen till ett program](grant-admin-consent.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](../develop/active-directory-v2-scopes.md)

För att få hjälp eller hitta svar på dina frågor:
* [Azure AD på StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
