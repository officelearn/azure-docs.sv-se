---
title: Förfallodatum för Office 365-grupper i Azure Active Directory | Microsoft Docs
description: Hur du ställer in förfallodatum för Office 365-grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: aa8551db8c3982fa61b3d4731a8aac3e2832f1b1
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurera en princip för Office 365-grupper

Genom att ange en princip för dem kan du nu hantera livscykeln för Office 365-grupper. Du kan ange princip för endast Office 365-grupper i Azure Active Directory (AD Azure). 

När du har angett en grupp att gälla:
-   Ägare av gruppen meddelas att förnya gruppen som snart upphör att gälla
-   En grupp som inte förnyas tas bort
-   En Office 365-grupp som tas bort kan återställas inom 30 dagar av gruppen ägare eller administratören

> [!NOTE]
> Konfigurera och använda en princip för Office 365-grupper måste du ha till hands Azure AD Premium-licenser för alla medlemmar i grupper som en princip används.

Information om hur du hämtar och installerar Azure AD PowerShell-cmdlets finns i [Azure Active Directory PowerShell för diagram 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Roller och behörigheter
Följande är de roller som kan konfigurera och använda giltighetstid för Office 365-grupper i Azure AD.

Roll | Behörighet
-------- | --------
Global administratör eller Användaradministratör för kontot | Kan skapa, läsa, uppdatera eller ta bort principinställningarna upphör att gälla för Office 365-grupper<br>Förnya alla Office 365-grupper
Användare | Förnya ett Office 365-grupp som de äger<br>Återställa en Office 365-grupp som de äger<br>Kan läsa upphör att gälla principinställningar

Mer information om behörigheter för att återställa en borttagen grupp finns [återställa en borttagen Office 365-grupp i Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Ange grupp upphör att gälla

1. Öppna den [administrationscentret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör i Azure AD-klienten.

2. Välj **grupper**och välj **giltighetstid** att öppna inställningarna för förfallodatum.
  
  ![Bladet upphör att gälla](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. På den **giltighetstid** bladet kan du:

  * Ange grupp-livstid i dagar. Du kan välja något av de fördefinierade värdena eller ett anpassat värde (ska vara 31 dagar eller mer). 
  * Ange en e-postadress där förnyelse och förfallodatum meddelanden ska skickas när en grupp har ingen ägare. 
  * Välj vilka Office 365-grupper att gälla. Du kan aktivera förfallodatum för **alla** Office 365-grupper som du kan välja att aktivera endast **valda** Office 365-grupper eller välja **ingen** att inaktivera upphör att gälla för alla grupper .
  * Spara dina inställningar när du är klar genom att välja **spara**.


E-postmeddelanden som den här skickas till Office 365 gruppen ägare 30 dagar, 15 dagar och 1 dag innan upphör att gälla i gruppen.

![Förfallodatum för e-postavisering](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Från den **förnya grupp** e-postmeddelande, gruppen ägare har direkt åtkomst till gruppen informationssidan på åtkomstpanelen. Där kan kan användarna få mer information om gruppen, till exempel dess beskrivning, när den senast förnyades, när den upphör att gälla och möjlighet att förnya gruppen. Informationssidan om grupp nu innehåller också länkar till gruppresurser Office 365 så att gruppägare lätt kan visa innehåll och aktiviteter i sin grupp.

När en grupp upphör att gälla bort gruppen en dag efter förfallodatumet. Ett e-postmeddelande som den här skickas till Office 365 gruppen ägare informerar dem om förfallodatum och efterföljande borttagning av deras Office 365-grupp.

![Gruppen borttagning e-postavisering](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Gruppen kan återställas inom 30 dagar från dess borttagningen genom att välja **återställning grupp** eller med hjälp av PowerShell-cmdlets som beskrivs i [återställa en borttagen Office 365-grupp i Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Om gruppen du återställa innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt, kan det ta upp till 24 timmar att fullständigt återställa gruppen och dess innehåll.

> [!NOTE]
> * När du först ställa in giltighetstid ställs alla grupper som är äldre än intervallet som upphör att gälla och 30 dagar tills upphör att gälla. Första förnyelse-e-postmeddelandet skickas ut inom en dag. 
>   Till exempel grupp A skapades 400 dagar sedan och förfallodatum intervallet anges till 180 dagar. När du använder inställningar för giltighetstid har grupp A 30 dagar innan den tas bort, såvida inte ägaren förnyar den.
> * För närvarande endast en princip kan konfigureras för Office 365-grupper på en klient.
> * När en dynamisk grupp tas bort och återställs, är det ses som en ny grupp och nytt fylls enligt regeln. Den här processen kan ta upp till 24 timmar.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Så här fungerar förfallodatum för Office 365-grupp med en postlåda i juridiskt bevarande
När en grupp upphör att gälla och raderas sedan 30 dagar efter borttagningen gruppens data från appar som Planner, platser, eller team raderas permanent, men grupp-postlåda som är av juridiska skäl behålls och tas inte bort permanent. Administratören kan använda Exchange-cmdlets för att återställa postlådan för att hämta data. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Så här fungerar förfallodatum för Office 365-grupp med bevarandeprincip
Bevarandeprincipen har konfigurerats med säkerhet och efterlevnad Center. Om du har ställt in en bevarandeprincip för Office 365-grupper, när en grupp upphör att gälla och tas bort, finns gruppen konversationer i gruppen postlåda och filer i gruppen plats kvar i behållaren kvarhållning för ett visst antal dagar som definierats i kvarhållning princip. Användarna ser gruppen eller dess innehåll efter förfallodatumet, men kan återställa platsen och postlåda data via e-identifiering.

## <a name="powershell-examples"></a>PowerShell-exempel
Här följer exempel på hur du kan använda PowerShell-cmdlets för att konfigurera inställningar för giltighetstid för Office 365-grupper i din klient:

1. Installera modulen PowerShell version 2.0 Preview (2.0.0.137) och logga in i PowerShell-Kommandotolken:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Konfigurera inställningar för giltighetstid ny AzureADMSGroupLifecyclePolicy: denna cmdlet anger livslängd för alla Office 365-grupper i klienten och 365 dagar. Förnyelse meddelanden för Office 365-grupper utan ägare kommer att skickas till 'emailaddress@contoso.com'
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Hämta den befintliga principen Get-AzureADMSGroupLifecyclePolicy: denna cmdlet hämtar de aktuella Office 365 giltighetstid gruppinställningar som har konfigurerats. I det här exemplet visas:
  * Princip-ID 
  * Livslängd för alla Office 365-grupper i klienten anges till 365 dagar
  * Förnyelse meddelanden för Office 365-grupper utan ägare kommer att skickas till 'emailaddress@contoso.com”.
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Uppdatera den befintliga principen Set AzureADMSGroupLifecyclePolicy: den här cmdleten används för att uppdatera en befintlig princip. I exemplet nedan ändrats grupp livslängd i den befintliga principen från 365 dagar till 180 dagar. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Lägga till specifika grupper i principen Lägg till AzureADMSLifecyclePolicyGroup: denna cmdlet lägger till en grupp till principen livscykel. Som exempel: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Ta bort den befintliga principen Remove-AzureADMSGroupLifecyclePolicy: den här cmdleten tar bort inställningarna för förfallodatum för Office 365 men kräver princip-ID Detta inaktiverar giltighetstid för Office 365-grupper. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
Följande cmdlets kan användas för att konfigurera principen i detalj. Mer information finns i [PowerShell dokumentationen](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Nästa steg
Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

* [Se befintliga grupper](active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](active-directory-groups-dynamic-membership-azure-portal.md)
