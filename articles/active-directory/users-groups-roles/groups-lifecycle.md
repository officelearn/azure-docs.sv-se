---
title: Ställa in ett utgångsdatum för Office 365 - grupper i Azure Active Directory | Microsoft Docs
description: Hur du ställer in förfallodatum för Office 365-grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e4585e8e1a809824e63f917fed1cc8a9cfa646a
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202936"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurera en princip för Office 365-grupper

Genom att ange en princip för dem kan du nu hantera livscykeln för Office 365-grupper. Du kan ange princip för endast Office 365-grupper i Azure Active Directory (AD Azure). 

När du har en grupp att upphöra att gälla:

- Ägare av gruppen får ett meddelande om att förnya gruppen som närmar sig förfallodatum
- En grupp som inte förnyas tas bort
- Alla Office 365-grupp som tas bort kan återställas inom 30 dagar av gruppägarna eller administratören

För närvarande kan endast en förfalloprincip konfigureras för Office 365-grupper i en klientorganisation.

> [!NOTE]
> Konfigurera och använda en princip för Office 365-grupper måste du ha till hands Azure AD Premium-licenser för alla medlemmar i grupper som en princip tillämpas.

Information om hur du hämtar och installerar Azure AD PowerShell-cmdlets finns i [Azure Active Directory PowerShell för Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Roller och behörigheter

Följande är roller som kan konfigurera och använda förfallodatum för Office 365-grupper i Azure AD.

Roll | Behörigheter
-------- | --------
Global administratör eller Användaradministratör | Kan skapa, läsa, uppdatera eller ta bort principinställningar för Office 365 grupper upphör att gälla<br>Kan förnya någon Office 365-grupp
Användare | Förnya ett Office 365-grupp som de äger<br>Återställa en Office 365-grupp som de äger<br>Kan läsa förfallodatum principinställningar

Läs mer om behörigheter för att återställa en borttagen grupp [återställa en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Öppna den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är en global administratör i Azure AD-klienten.

2. Välj **grupper**och välj sedan **upphör att gälla** att öppna inställningar för giltighetstid.
  
   ![Inställningar för giltighetstid för grupper](./media/groups-lifecycle/expiration-settings.png)

3. På den **upphör att gälla** bladet kan du:

  * Ange gruppens livstid i dagar. Du kan välja någon av de förinställda värdena, eller ett anpassat värde (bör vara 31 dagar eller mer). 
  * Ange en e-postadress där förnyelse och förfallodatum meddelanden ska skickas när en grupp har ingen ägare. 
  * Välj vilka Office 365-grupper går ut. Du kan aktivera förfallotid för **alla** Office 365-grupper som du kan välja att aktivera endast **valda** Office 365-grupper, eller välj **ingen** att inaktivera upphör att gälla för alla grupper .
  * Spara dina inställningar när du är klar genom att välja **spara**.

> [!NOTE]
> * När du först konfigurerar upphör att gälla är grupper som är äldre än giltighetsintervallet inställda på 30 dagar kvar till förfallodatum. Första förnyelse-e-postmeddelandet skickas ut inom en dag. Till exempel grupp A skapades 400 dagar sedan och giltighetsintervallet är inställd på 180 dagar. När du tillämpar inställningar för giltighetstid har 30 dagar innan de tas bort i grupp A Om inte ägaren förnyar den.
> * När en dynamisk grupp tagits bort och återställts visas den som en ny grupp och fylls i baserat på regeln. Den här processen kan ta upp till 24 timmar.

## <a name="email-notifications"></a>E-postmeddelanden

E-postmeddelanden som den här skickas till Office 365-gruppägare 30 dagar, 15 dagar och 1 dag före förfallodatumet i gruppen. Språket för e-postmeddelandet bestäms av grupper i det språk du föredrar eller klient språk. Om gruppägaren har definierat ett språk du föredrar, eller flera ägare har samma önskat språk, används det språket. Klient språk används för alla andra fall.

![Upphör att gälla e-postaviseringar](./media/groups-lifecycle/expiration-notification.png)

Från den **förnya grupp** e-postmeddelande, gruppen ägare kan direkt komma åt gruppsidan i åtkomstpanelen. Där kan kan användarna få mer information om gruppen, till exempel en beskrivning, när den senast har förnyats, när den upphör och möjligheten att förnya gruppen. Gruppsidan nu innehåller också länkar till Office 365 gruppera resurser, så att gruppägare kan enkelt visa innehåll och aktivitet i gruppen.

När en grupp upphör att gälla, är gruppen bort en dag efter utgångsdatum. Ett e-postmeddelande som följande skickas till Office 365-gruppägare som informerar dem om förfallodatum och kommande borttagning av deras Office 365-grupp.

![Gruppen borttagning av e-postaviseringar](./media/groups-lifecycle/deletion-notification.png)

Gruppen kan återställas inom 30 dagar från dess borttagningen genom att välja **Återställ grupp** eller med hjälp av PowerShell-cmdletar, enligt beskrivningen i [återställa en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md).
    
Om gruppen som du återställa innehåller dokument, SharePoint-webbplatser och andra beständiga objekt, kan det ta upp till 24 timmar att fullständigt återställa gruppen och dess innehåll.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Hur du hämtar förfallodatum för Office 365-grupp
Förutom åtkomstpanelen där användare kan visa gruppinformation inklusive upphör att gälla och datum för senaste förnyade, kan utgångsdatumet för en Office 365-grupp hämtas från Microsoft Graph REST API Beta. expirationDateTime som en gruppegenskap har aktiverats i betaversionen av Microsoft Graph. De kan hämtas med en GET-begäran. Mer information finns i [det här exemplet](https://docs.microsoft.com/en-us/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> ”Begränsa åtkomsten till grupper i åtkomstpanelen” måste vara inställt på ”Nej” i Azure Active Directory-grupper allmänna inställningar för att kunna hantera gruppmedlemskap på åtkomstpanelen.


## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hur Office 365 förfallodatum fungerar med en postlåda av juridiska skäl
När en grupp upphör att gälla och tas bort, sedan 30 dagar efter borttagningen gruppens data från appar som Planner, platser, eller team raderas, men grupp-postlådan som finns på bevarande av juridiska skäl finns kvar och tas inte bort permanent. Administratören kan använda Exchange-cmdlets för att återställa postlådan för att hämta data. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Hur Office 365 förfallodatum fungerar med bevarandeprincip
Bevarandeprincipen konfigureras via Security and Compliance Center. Om du har ställt in en bevarandeprincip för Office 365-grupper, när en grupp upphör att gälla och tas bort, gruppkonversationer i grupp-postlåda och filer i grupp-platsen ska finnas kvar i behållaren kvarhållning för ett visst antal dagar som definierats i kvarhållning princip. Användarna ser inte gruppen eller dess innehåll efter förfallodatumet, men kan återställa platsen och postlåda data via e-discovery.

## <a name="powershell-examples"></a>PowerShell-exempel
Här följer exempel på hur du kan använda PowerShell-cmdletar för att konfigurera inställningar för giltighetstid för Office 365-grupper i din klient:

1. Installera PowerShell v2.0-förhandsversionsmodulen (2.0.0.137) och logga in i PowerShell-Kommandotolken:
   ```powershell
   Install-Module -Name AzureADPreview
   connect-azuread 
   ```
2. Konfigurera inställningar för giltighetstid New-AzureADMSGroupLifecyclePolicy:  Den här cmdlet: en anger livslängden för alla Office 365-grupper i klienten och 365 dagar. Förnyelse av meddelanden för Office 365 grupper utan ägare kommer att skickas till 'emailaddress@contoso.com'
  
   ```powershell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```
3. Hämta den befintliga principen Get-AzureADMSGroupLifecyclePolicy: Denna cmdlet hämtar de aktuella upphör att gälla inställningarna för Office 365-grupp som har konfigurerats. I det här exemplet ser du:
   * Princip-ID 
   * Livslängd för alla Office 365-grupper i klienten är inställd på 365 dagar
   * Förnyelse av meddelanden för Office 365 grupper utan ägare kommer att skickas till ”emailaddress@contoso.com”.
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ``` 
   
4. Uppdatera den befintliga principen Set-AzureADMSGroupLifecyclePolicy: Denna cmdlet används för att uppdatera en befintlig princip. I exemplet nedan ändras grupp livstid i den befintliga principen från 365 dagar till 180 dagar. 
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
5. Lägga till specifika grupper i principen Add-AzureADMSLifecyclePolicyGroup: Denna cmdlet lägger till en grupp i policyn för onlinelivscykeln. Som exempel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
6. Ta bort den befintliga principen Remove-AzureADMSGroupLifecyclePolicy: Denna cmdlet tar bort förfallodatum för Office 365 gruppinställningar men kräver princip-ID. Detta inaktiverar förfallodatum för Office 365-grupper. 
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Följande cmdletar kan användas för att konfigurera principen i detalj. Mer information finns i [PowerShell-dokumentationen](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

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

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
