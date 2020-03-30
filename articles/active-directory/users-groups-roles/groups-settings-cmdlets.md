---
title: Konfigurera gruppinställningar med PowerShell - Azure AD | Microsoft-dokument
description: Så här hanterar du inställningarna för grupper som använder Azure Active Directory-cmdlets
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/20/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d56bb7c30a8289fe7f261979dca6a4ffe2bfe99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048145"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-cmdletar för att konfigurera gruppinställningar

Den här artikeln innehåller instruktioner för hur du använder Azure Active Directory (Azure AD) PowerShell-cmdlets för att skapa och uppdatera grupper. Det här innehållet gäller endast Office 365-grupper (kallas ibland enhetliga grupper).

> [!IMPORTANT]
> Vissa inställningar kräver en Azure Active Directory Premium P1-licens. Mer information finns i tabellen [Mallinställningar.](#template-settings)

Om du vill ha mer information om hur du `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` förhindrar att användare som inte är administratörer skapar säkerhetsgrupper anger du enligt beskrivningen i [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0).

Inställningarna för Office 365-grupper konfigureras med ett Settings-objekt och ett SettingsTemplate-objekt. Inledningsvis visas inga inställningar i katalogen, eftersom katalogen är konfigurerad med standardinställningarna. Om du vill ändra standardinställningarna måste du skapa ett nytt inställningsobjekt med hjälp av en inställningsmall. Inställningsmallar definieras av Microsoft. Det finns flera olika inställningsmallar. Om du vill konfigurera gruppinställningar för Office 365 för katalogen använder du mallen "Group.Unified". Om du vill konfigurera gruppinställningar för Office 365 i en enskild grupp använder du mallen "Group.Unified.Guest". Den här mallen används för att hantera gäståtkomst till en Office 365-grupp. 

Cmdlets är en del av Azure Active Directory PowerShell V2-modulen. Instruktioner om hur du hämtar och installerar modulen på datorn finns i artikeln [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Du kan installera versionen 2-versionen av modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

Var noga med att avinstallera alla äldre versioner av Azure Active Directory PowerShell för Graph Module för Windows PowerShell och installera [Azure Active Directory PowerShell för Graph - Public Preview Release (senare än 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) innan du kör PowerShell-kommandona.

1. Öppna Windows PowerShell-appen som administratör.
2. Avinstallera en eventuell tidigare version av AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Installera den senaste versionen av AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Skapa inställningar på katalognivå
I de här stegen skapas inställningar på katalognivå som gäller för alla Office 365-grupper i katalogen. Cmdleten Get-AzureDirectorySettingTemplate är endast tillgänglig i [Azure AD PowerShell Preview-modulen för Graph](https://www.powershellgallery.com/packages/AzureADPreview).

1. I cmdlets-katalogerna DirectorySettings måste du ange ID för den SettingsTemplate som du vill använda. Om du inte känner till det här ID:et returneras listan över alla inställningsmallar i den här cmdleten:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Det här cmdlet-anropet returnerar alla mallar som är tillgängliga:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Om du vill lägga till en URL för användningsriktlinje måste du först hämta objektet SettingsTemplate som definierar URL-värdet för användningsriktlinje. det vill ha, mallen Group.Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Skapa sedan ett nytt inställningsobjekt baserat på mallen:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Uppdatera sedan värdet för användningsriktlinjen:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Använd sedan inställningen:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Du kan läsa värdena med hjälp av:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Uppdatera inställningar på katalognivå
Om du vill uppdatera värdet för UsageGuideLinesUrl i inställningsmallen läser du de aktuella inställningarna från Azure AD, annars kan vi sluta skriva över andra inställningar än UsageGuideLinesUrl.

1. Hämta de aktuella inställningarna från Group.Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Kontrollera de aktuella inställningarna:
   
   ```powershell
   $Setting.Values
   ```
   
   Resultat:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               false
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Om du vill ta bort värdet för UsageGuideLinesUrl redigerar du URL:en till en tom sträng:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Spara uppdatering i katalogen:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Mallinställningar
Här är de inställningar som definieras i Group.Unified SettingsTemplate. Om inget annat anges kräver dessa funktioner en Azure Active Directory Premium P1-licens. 

| **Inställning** | **Beskrivning** |
| --- | --- |
|  <ul><li>Aktivera koncernrekreation<li>Typ: Boolean<li>Standard: Sant |Flaggan som anger om skapa Office 365-grupp tillåts i katalogen av användare som inte är administratörer. Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GruppskapelseEfterdömdGroupId<li>Typ: Sträng<li>Standard: "" |GUID för den säkerhetsgrupp som medlemmarna tillåts skapa Office 365-grupper för även när EnableGroupCreation == falskt. |
|  <ul><li>AnvändningGuidelinsUrl<li>Typ: Sträng<li>Standard: "" |En länk till riktlinjerna för gruppanvändning. |
|  <ul><li>KlassificeringBeskrivningar<li>Typ: Sträng<li>Standard: "" | En kommaavgränsad lista med klassificeringsbeskrivningar. Värdet för ClassificationDescriptions är endast giltigt i det här formatet:<br>$setting["ClassificationDescriptions"] ="Klassificering:Beskrivning,Klassificering:Beskrivning"<br>där Klassificering matchar en post i klassificeringslistan.<br>Den här inställningen gäller inte när EnableMIPLabels == Sant.|
|  <ul><li>Standardklassificering<li>Typ: Sträng<li>Standard: "" | Den klassificering som ska användas som standardklassificering för en grupp om ingen har angetts.<br>Den här inställningen gäller inte när EnableMIPLabels == Sant.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Sträng<li>Standard: "" | Sträng med en maximal längd på 64 tecken som definierar namnkonventionen som konfigurerats för Office 365-grupper. Mer information finns i [Tillämpa en namngivningsprincip för Office 365-grupper](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: Sträng<li>Standard: "" | Kommaavgränsad sträng med fraser som användare inte får använda i gruppnamn eller alias. Mer information finns i [Tillämpa en namngivningsprincip för Office 365-grupper](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: Boolean<li>Standard: "Falskt" | Använd inte
|  <ul><li>TillåtguestsToBeGroupOwner<li>Typ: Boolean<li>Standard: Falskt | Boolesk som anger om en gästanvändare kan vara ägare till grupper. |
|  <ul><li>Tillåtgästertillåterbehörgrupper<li>Typ: Boolean<li>Standard: Sant | Boolesk som anger om en gästanvändare kan ha tillgång till Innehåll i Office 365-grupper eller inte.  Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GästUsageGuidelinesUrl<li>Typ: Sträng<li>Standard: "" | Webbadressen till en länk till riktlinjerna för gästanvändning. |
|  <ul><li>Tillåt attlägga tillgäster<li>Typ: Boolean<li>Standard: Sant | En boolesk som anger om det är tillåtet att lägga till gäster i den här katalogen. <br>Den här inställningen kan åsidosättas och vara skrivskyddad om *EnableMIPLabels* är inställt på *Sant* och en gästprincip är associerad med känslighetsetiketten som tilldelats gruppen.<br>Om inställningen AllowToAddGuests är inställd på False på klientnivå ignoreras alla AllowToAddGuests-inställningar på gruppnivå. Om du bara vill aktivera gäståtkomst för ett fåtal grupper måste du ange att AllowToAddGuests ska vara true på klientnivå och sedan selektivt inaktivera den för specifika grupper. |
|  <ul><li>Klassificeringslista<li>Typ: Sträng<li>Standard: "" | En kommaavgränsad lista över giltiga klassificeringsvärden som kan tillämpas på Office 365-grupper. <br>Den här inställningen gäller inte när EnableMIPLabels == Sant.|
|  <ul><li>EnableMIPLabels<li>Typ: Boolean<li>Standard: "Falskt" |Flaggan som anger om känslighetsetiketter som publicerats i Microsoft 365 Compliance Center kan tillämpas på Office 365-grupper. Mer information finns i [Tilldela känslighetsetiketter för Office 365-grupper](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exempel: Konfigurera gästprincip för grupper på katalognivå
1. Hämta alla inställningsmallar:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Om du vill ange gästprincip för grupper på katalognivå behöver du mallen Gruppera.Enhetlig
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Skapa sedan ett nytt inställningsobjekt baserat på mallen:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Uppdatera sedan inställningen AllowToAddGuests
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Använd sedan inställningen:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Du kan läsa värdena med hjälp av:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Läsinställningar på katalognivå

Om du känner till namnet på den inställning som du vill hämta kan du använda cmdlet under nedan för att hämta det aktuella inställningsvärdet. I det här exemplet hämtar vi värdet för en inställning med namnet "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
Dessa steg läsinställningar på katalognivå, som gäller för alla Office-grupper i katalogen.

1. Läs alla befintliga kataloginställningar:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Den här cmdleten returnerar en lista över alla kataloginställningar:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Läs alla inställningar för en viss grupp:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Läs alla kataloginställningarsvärden för ett visst kataloginställningsobjekt med hjälp av Inställningar ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Den här cmdleten returnerar namnen och värdena i det här inställningsobjektet för den här specifika gruppen:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Ta bort inställningar på katalognivå
Det här steget tar bort inställningar på katalognivå, som gäller för alla Office-grupper i katalogen.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Skapa inställningar för en viss grupp

1. Sök efter inställningsmallen "Groups.Unified.Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Hämta mallobjektet för mallen Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Skapa ett nytt inställningsobjekt från mallen:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ange inställningen till önskat värde:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Hämta ID:t för den grupp som du vill använda den här inställningen på:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Skapa den nya inställningen för den grupp som krävs i katalogen:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Om du vill kontrollera inställningarna kör du det här kommandot:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Uppdatera inställningar för en viss grupp
1. Hämta ID:t för den grupp vars inställning du vill uppdatera:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Hämta inställningen för gruppen:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Uppdatera inställningen för gruppen efter behov, t.ex.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Hämta sedan ID:t för inställningen för den här specifika gruppen:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Du kommer att få ett svar som liknar detta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Sedan kan du ange det nya värdet för den här inställningen:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Du kan läsa värdet för inställningen för att se till att den har uppdaterats korrekt:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Syntaxreferens för cmdlet
Du hittar mer Azure Active Directory PowerShell-dokumentation på [Azure Active Directory Cmdlets](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Ytterligare läsning

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
