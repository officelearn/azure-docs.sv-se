---
title: Snabbstart om att lägga till namngivningsprincip för Office 365-grupper – Azure Active Directory | Microsoft Docs
description: Beskriver hur du lägger till nya användare eller tar bort befintliga användare i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4105fa17041c7cefd1387d1ee50c177b8c55fc9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651294"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Snabbstart: Namnprincip för grupper i Azure Active Directory

I den här snabbstarten konfigurerar du en namnprincip i din Azure Active Directory-klientorganisationen (Azure AD) för användarskapade Office 365-grupper för att hjälpa dig att sortera och söka i klientorganisationens grupper. Du kan till exempel använda namnprincipen till att:

* Kommunicera funktionen för en grupp, ett medlemskap, en geografisk region eller vem som har skapat gruppen.
* Kategorisera grupper i adressboken.
* Blockera specifika ord från att användas i gruppnamn och alias.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

Se till att avinstallera äldre versioner av Azure Active Directory PowerShell för Graph-modulen för Module for Windows PowerShell och installera [Azure Active Directory PowerShell för Graph – offentlig förhandsversion 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) innan du kör PowerShell-kommandon. 

1. Öppna Windows PowerShell-appen som administratör.
2. Avinstallera en eventuell tidigare version av AzureADPreview.
  

   ```powershell
   Uninstall-Module AzureADPreview
   ```

3. Installera den senaste versionen av AzureADPreview.
  

   ```powershell
   Install-Module AzureADPreview
   ```

   Om du får ett meddelande om åtkomst till en icke-betrodd lagringsplats anger du **Y** (Ja). Det kan ta några minuter för den nya modulen att installeras.

## <a name="set-up-naming-policy"></a>Konfigurera namnprincip

### <a name="step-1-sign-in-using-powershell-cmdlets"></a>Steg 1: Logga in med PowerShell-cmdletar

1. Öppna Windows PowerShel- appen. Du behöver inte förhöjd behörighet.

2. Kör följande kommandon för att förbereda körningen av cmdletarna.
  

   ```powershell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   På sidan **Logga in på ditt konto** som öppnas anger du ditt administratörskonto och lösenord för att ansluta till tjänsten, och väljer **Logga in**.

3. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera gruppinställningar](groups-settings-cmdlets.md) för att skapa gruppinställningar för den här klientorganisationen.

### <a name="step-2-view-the-current-settings"></a>Steg 2: Visa de aktuella inställningarna

1. Visa de aktuella namnprincipinställningarna.
  

   ```powershell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id
   ```

  
2. Visa de aktuella gruppinställningarna.
  

   ```powershell
   $Setting.Values
   ```

  

### <a name="step-3-set-the-naming-policy-and-any-custom-blocked-words"></a>Steg 3: Ange namnprincipen och eventuella anpassade blockerade ord

1. Ange gruppnamnsprefix och -suffix i Azure AD PowerShell. För att funktionen ska fungera korrekt måste [GroupName] inkluderas i inställningen.
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```

  
2. Ange de anpassade blockerade ord du vill begränsa. I följande exempel visas hur du kan lägga till dina egna anpassade ord.
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```

  
3. Spara inställningarna för att den nya principen ska gälla, som i följande exempel.
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

  
Klart! Du har angett en namnprincip och lagt till dina anpassade blockerade ord.

## <a name="clean-up-resources"></a>Rensa resurser

1. Ta bort gruppnamnsprefix och -suffix i Azure AD PowerShell.
  

   ```powershell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```

  
2. Ta bort de anpassade spärrade orden.
  

   ```powershell
   $Setting["CustomBlockedWordsList"]=""
   ```

  
3. Spara inställningarna.
  

   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | Where-Object -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder PowerShell-cmdletar för att ange namnprincipen för din Azure AD-klientorganisation.

Mer information om tekniska begränsningar, att lägga till en lista med anpassade blockerade ord eller slutanvändargränssnitten i Office 365-appar finns i nästa artikel, där du kan läsa mer om namnprincipens detaljer.
> [!div class="nextstepaction"]
> [All information om namnprinciper](groups-naming-policy.md)