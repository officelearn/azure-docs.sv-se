---
title: "Konfigurera inställningar för grupper med PowerShell i Azure Active Directory | Microsoft Docs"
description: "Hur hanterar inställningar för grupper med hjälp av Azure Active Directory-cmdlets"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 9f2090e6-3af4-4f07-bbb2-1d18dae89b73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro;
ms.openlocfilehash: 331dafc9164e315c84036fa0af11820e89066f36
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-cmdletar för att konfigurera gruppinställningar
Den här artikeln innehåller anvisningar för att skapa och uppdatera grupper med Azure Active Directory (AD Azure) PowerShell-cmdlets. Det här innehållet gäller endast för Office 365-grupper. 

> [!IMPORTANT]
> Vissa inställningar kräver en Azure Active Directory Premium P1-licens. Mer information finns i [mallinställningar](#template-settings) tabell.

Mer information om hur du förhindra att icke-administratörer att skapa *säkerhet* grupper, ange `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` enligt beskrivningen i [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Inställningar för Office 365-grupper har konfigurerats med ett inställningsobjekt och ett SettingsTemplate-objekt. Inledningsvis visas alla för inställningsobjekt i katalogen, eftersom katalogen är konfigurerad med standardinställningar. Om du vill ändra standardinställningarna, måste du skapa ett nytt inställningsobjekt med en mall för inställningar. Inställningar för mallar har definierats av Microsoft. Det finns flera olika inställningar mallar. Om du vill konfigurera inställningar för din katalog i Office 365-grupper kan du använda mallen med namnet ”Group.Unified”. Använd mallen med namnet ”Group.Unified.Guest” om du vill konfigurera inställningar för Office 365-grupper på en enda grupp. Denna mall används för att hantera gäståtkomst till en grupp för Office 365. 

Cmdletarna som ingår i Azure Active Directory PowerShell V2-modulen. Mer information hur du kan hämta och installera modulen på datorn finns i artikeln [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Du kan installera med version 2 av modul från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="retrieve-a-specific-settings-value"></a>Hämta ett värde för specifika inställningar
Om du känner till namnet på den inställning som du vill hämta kan du använda den nedan för att hämta det aktuella Inställningsvärdet. I det här exemplet hämtar vi värdet för en inställning med namnet ”UsageGuidelinesUrl”. Du kan läsa mer om inställningar och deras namn ytterligare ned i den här artikeln.

```powershell
(Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
```

## <a name="create-settings-at-the-directory-level"></a>Skapa inställningar på katalognivå
De här stegen skapa inställningar på katalognivå, som gäller för alla Office 365-grupper (Unified grupper) i katalogen.

1. Du måste ange ID för SettingsTemplate som du vill använda i DirectorySettings-cmdlets. Om du inte vet detta ID returnerar denna cmdlet listan över alla mallar för inställningarna:
  
  ```
  PS C:> Get-AzureADDirectorySettingTemplate
  ```
  Det här anropet cmdlet returnerar alla mallar som är tillgängliga:
  
  ```
  Id                                   DisplayName         Description
  --                                   -----------         -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Unified Group
  16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
  ```
2. Om du vill lägga till en riktlinje URL för användning, måste du först hämta SettingsTemplate-objektet som definierar användning riktlinje URL värde. det vill säga Group.Unified mallen:
  
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
  ```
3. Därefter skapa ett nytt för inställningsobjekt som baseras på mallen:
  
  ```
  $Setting = $template.CreateDirectorySetting()
  ```  
4. Uppdatera sedan riktlinje användarvärde:
  
  ```
  $setting["UsageGuidelinesUrl"] = "<https://guideline.com>"
  ```  
5. Slutligen tillämpas inställningarna:
  
  ```
  New-AzureADDirectorySetting -DirectorySetting $setting
  ```

Vid slutförande returnerar cmdleten ID för det nya inställningsobjektet:
  ```
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323             62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```
## <a name="template-settings"></a>Mallinställningar
Här är de inställningar som definierats i Group.Unified SettingsTemplate. Om inget annat anges kräver dessa funktioner en Azure Active Directory Premium P1-licens. 

| **Inställning** | **Beskrivning** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: booleskt<li>Standard: True |Flagga som anger om skapande av en enhetlig grupp tillåts i katalogen med icke-administratörer. Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Sträng<li>Standard ”:” |GUID för säkerhetsgruppen för vilka medlemmar som tillåts skapa enhetlig grupper även om EnableGroupCreation == false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: Sträng<li>Standard ”:” |En länk till riktlinjer för grupp-användning. |
|  <ul><li>ClassificationDescriptions<li>Typ: Sträng<li>Standard ”:” | En kommaavgränsad lista över klassificering beskrivningar. |
|  <ul><li>DefaultClassification<li>Typ: Sträng<li>Standard ”:” | Den klassificering som ska användas som standard klassificeringen för en grupp om inget har angetts.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Sträng<li>Standard ”:” | Använd inte. Inte implementerat. |
| <ul><li>CustomBlockedWordsList<li>Typ: Sträng<li>Standard ”:” | Använd inte. Inte implementerat. |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: booleskt<li>Standard: ”False” | Använd inte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: booleskt<li>Standard: False | Booleskt värde som anger huruvida en gästanvändare kan vara en ägare av grupper. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: booleskt<li>Standard: True | Booleskt värde som anger huruvida en gästanvändare kan ha åtkomst till innehåll för Unified grupper.  Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: Sträng<li>Standard ”:” | Url till en länk till riktlinjer för gäst-användning. |
|  <ul><li>AllowToAddGuests<li>Typ: booleskt<li>Standard: True | Ett booleskt värde som anger om eller inte är tillåtet att lägga till gäster i den här katalogen.|
|  <ul><li>ClassificationList<li>Typ: Sträng<li>Standard ”:” |En kommaavgränsad lista över giltiga klassificeringsvärden som kan tillämpas på Unified grupper. |


## <a name="read-settings-at-the-directory-level"></a>Läsinställningar på katalognivå
De här stegen läsa inställningar på katalognivå, som gäller för alla Office-grupper i katalogen.

1. Läsa alla befintliga directory-inställningar:
  ```
  Get-AzureADDirectorySetting -All $True
  ```
  Denna cmdlet returnerar en lista över alla inställningar:
  ```
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  ```

2. Läsa alla inställningar för en viss grupp:
  ```
  Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
  ```

3. Läsa alla värden som directory inställningar i en specifik katalog settings-objekt med hjälp av inställningarna för Id-GUID:
  ```
  (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
  ```
  Denna cmdlet returnerar namn och värden i det här inställningsobjektet för den här specifika gruppen:
  ```
  Name                          Value
  ----                          -----
  ClassificationDescriptions
  DefaultClassification
  PrefixSuffixNamingRequirement
  AllowGuestsToBeGroupOwner     False 
  AllowGuestsToAccessGroups     True
  GuestUsageGuidelinesUrl
  GroupCreationAllowedGroupId
  AllowToAddGuests              True
  UsageGuidelinesUrl            <https://guideline.com>
  ClassificationList
  EnableGroupCreation           True
  ```

## <a name="update-settings-for-a-specific-group"></a>Uppdatera inställningarna för en specifik grupp

1. Söka efter inställningar mallen med namnet ”Groups.Unified.Guest”
  ```
  Get-AzureADDirectorySettingTemplate
  
  Id                                   DisplayName            Description
  --                                   -----------            -----------
  62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
  08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Unified Group
  4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
  898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
  5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
  ```
2. Hämta mallobjektet för Groups.Unified.Guest mallen:
  ```
  $Template = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
  ```
3. Skapa ett nytt inställningsobjekt från mallen:
  ```
  $Setting = $Template.CreateDirectorySetting()
  ```

4. Ange om du obligatoriskt värde:
  ```
  $Setting["AllowToAddGuests"]=$False
  ```
5. Skapa den nya inställningen för den nödvändiga gruppen i katalogen:
  ```
  New-AzureADObjectSetting -TargetType Groups -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -DirectorySetting $Setting
  
  Id                                   DisplayName TemplateId                           Values
  --                                   ----------- ----------                           ------
  25651479-a26e-4181-afce-ce24111b2cb5             08d542b9-071f-4e16-94b0-74abb372e3d9 {class SettingValue {...
  ```

## <a name="update-settings-at-the-directory-level"></a>Uppdatera inställningar på katalognivå

De här stegen uppdatera inställningar på katalognivå, som gäller för alla Unified grupper i katalogen. De här exemplen antar att det finns redan ett inställningsobjekt i din katalog.

1. Hitta objektet befintliga inställningar:
  ```
  Get-AzureADDirectorySetting | Where-object -Property Displayname -Value "Group.Unified" -EQ
  
  Id                                   DisplayName   TemplateId                           Values
  --                                   -----------   ----------                           ------
  c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
  
  $setting = Get-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323
  ```
2. Uppdatera värdet:
  
  ```
  $Setting["AllowToAddGuests"] = "false"
  ```
3. Uppdatera inställningen:
  
  ```
  Set-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323 -DirectorySetting $Setting
  ```

## <a name="remove-settings-at-the-directory-level"></a>Ta bort inställningar på katalognivå
Det här steget tar bort inställningar på katalognivå, som gäller för alla Office-grupper i katalogen.
  ```
  Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
  ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet-referens för syntax
Du kan hitta mer Azure Active Directory PowerShell-dokumentationen på [Azure Active Directory-cmdletarna](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Ytterligare resurser

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](active-directory-manage-groups.md)
* [Integrera dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md)
