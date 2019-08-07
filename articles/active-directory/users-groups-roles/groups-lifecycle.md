---
title: Ange förfallo datum för Office 365-grupper – Azure Active Directory | Microsoft Docs
description: Så här konfigurerar du förfallo datum för Office 365-grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074b9ec06818363a97253a587ac451a38999832f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837937"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurera princip för förfallo datum för Office 365-grupper

Den här artikeln beskriver hur du hanterar livs cykeln för Office 365-grupper genom att ange en förfallo princip för dem. Du kan ange förfallo princip för endast Office 365-grupper i Azure Active Directory (Azure AD).

När du har angett att en grupp upphör att gälla:

- Ägare av gruppen meddelas om att förnya gruppen under tiden närmast
- Alla grupper som inte förnyas tas bort
- Alla Office 365-grupper som tas bort kan återställas inom 30 dagar av gruppen ägare eller administratören

För närvarande kan endast en förfalloprincip konfigureras för Office 365-grupper i en klientorganisation.

> [!NOTE]
> Genom att konfigurera och använda principen för förfallo datum för Office 365-grupper måste du ha Azure AD Premium licenser för medlemmarna i alla grupper som förfallo principen tillämpas för.

Information om hur du hämtar och installerar Azure AD PowerShell-cmdlets finns i [Azure Active Directory PowerShell för Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="roles-and-permissions"></a>Roller och behörigheter

Följande är roller som kan konfigurera och använda förfallo datum för Office 365-grupper i Azure AD.

Role | Behörigheter
-------- | --------
Global administratör eller användar administratör | Kan skapa, läsa, uppdatera eller ta bort princip inställningarna för utgångs princip i Office 365-grupper<br>Kan förnya valfri Office 365-grupp
Användare | Kan förnya en Office 365-grupp som de äger<br>Kan återställa en Office 365-grupp som de äger<br>Kan läsa princip inställningarna för förfallo datum

Mer information om behörigheter för att återställa en borttagen grupp finns i [återställa en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Öppna [administrations centret för Azure AD](https://aad.portal.azure.com) med ett konto som är en global administratör i din Azure AD-organisation.

2. Välj **grupper**och välj sedan **förfallo datum** för att öppna inställningarna för förfallo datum.
  
   ![Förfallo inställningar för grupper](./media/groups-lifecycle/expiration-settings.png)

3. På sidan **förfallo datum** kan du:

  - Ange gruppens livs längd i dagar. Du kan välja ett av de förinställda värdena eller ett anpassat värde (bör vara 31 dagar eller mer).
  - Ange en e-postadress där förnyelse-och förfallo meddelanden ska skickas när en grupp saknar ägare.
  - Välj vilka Office 365-grupper som upphör att gälla. Du kan ange förfallo datum för:
    - **Alla** Office 365-grupper
    - En lista med **valda** Office 365-grupper
    - **Ingen** för att begränsa förfallo datum för alla grupper
  - Spara inställningarna när du är klar genom att välja **Spara**.

> [!NOTE]
> När du först konfigurerar förfallo datum är alla grupper som är äldre än utgångs intervallet inställda på 30 dagar tills de upphör att gälla. Det första meddelandet om förnyad e-post skickas ut inom en dag. Till exempel har grupp A skapats för 400 dagar sedan och förfallo intervallet är inställt på 180 dagar. När du tillämpar princip för förfallo datum har grupp A 30 dagar innan den tas bort, om inte ägaren förnyar den.
> När en dynamisk grupp tas bort och återställs, visas den som en ny grupp och fylls i igen enligt regeln. Den här processen kan ta upp till 24 timmar.

## <a name="email-notifications"></a>E-postmeddelanden

E-postmeddelanden som det här skickas till grupp ägare i Office 365-gruppen 30 dagar, 15 dagar och 1 dag innan gruppen upphör att gälla. E-postmeddelandets språk bestäms av grupp ägarens önskade språk eller språk inställning för Azure AD. Om grupp ägaren har definierat ett önskat språk, eller om flera ägare har samma föredragna språk, används det språket. I alla andra fall används språk inställningen för Azure AD.

![E-postmeddelanden för förfallo datum](./media/groups-lifecycle/expiration-notification.png)

Från e-postmeddelandet **förnya grupp** kan grupp ägare direkt komma åt grupp informations sidan på åtkomst panelen. Användarna kan få mer information om gruppen, till exempel en beskrivning, när den senast förnyades, när den upphör att gälla och även möjligheten att förnya gruppen. På sidan grupp information finns nu även länkar till grupp resurserna för Office 365, så att grupp ägaren enkelt kan se innehållet och aktiviteten i gruppen.

När en grupp upphör att gälla tas gruppen bort en dag efter förfallo datumet. Ett e-postmeddelande, till exempel detta, skickas till grupp ägarna i Office 365 som informerar dem om förfallo datum och efterföljande borttagning av sin Office 365-grupp.

![E-postmeddelanden för grupp borttagning](./media/groups-lifecycle/deletion-notification.png)

Gruppen kan återställas inom 30 dagar från dess borttagning genom att välja **återställnings grupp** eller med hjälp av PowerShell-cmdletar, enligt beskrivningen i [återställa en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md). Observera att återställnings perioden på 30 dagar inte kan anpassas.

Om gruppen som du återställer innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt kan det ta upp till 24 timmar att helt återställa gruppen och dess innehåll.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Hämta utgångs datum för Office 365-gruppen
Förutom åtkomst panelen där användare kan visa grupp information, inklusive förfallo datum och senaste förnyelse datum, kan utgångs datumet för en Office 365-grupp hämtas från Microsoft Graph REST API beta. expirationDateTime som en grupp egenskap har Aktiver ATS i Microsoft Graph beta. Den kan hämtas med en GET-begäran. Mer information finns i [det här exemplet](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> För att kunna hantera grupp medlemskap på åtkomst panelen måste "begränsa åtkomst till grupper i åtkomst panelen" anges till "nej" i Azure Active Directory grupper generell inställning.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Hur Office 365-gruppen upphör att fungera med en post låda för juridiskt bevarande
När en grupp upphör att gälla och tas bort, kommer 30 dagar efter att gruppens data har tagits bort från appar som planerar, webbplatser eller team tas bort permanent, men grupp post lådan som är i juridiskt bevarande behålls och tas inte bort permanent. Administratören kan använda Exchange-cmdletar för att återställa post lådan för att hämta data. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Hur Office 365-gruppen upphör att fungera med bevarande principer
Bevarande principen konfigureras med hjälp av Security and Compliance Center. Om du har skapat en bevarande princip för Office 365-grupper, när en grupp upphör att gälla och tas bort, behålls gruppens konversationer i gruppens post låda och filer på grupp webbplatsen i kvarhållning-behållaren för det angivna antalet dagar definierade i kvarhållning politik. Användare ser inte gruppen eller dess innehåll efter förfallo datum, men kan återställa plats-och Mailbox-data via e-identifiering.

## <a name="powershell-examples"></a>PowerShell-exempel
Här följer några exempel på hur du kan använda PowerShell-cmdlets för att konfigurera förfallo inställningarna för Office 365-grupper i din Azure AD-organisation:

1. Installera PowerShell v 2.0-modulen och logga in i PowerShell-prompten:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurera förfallo inställningarna Använd cmdleten New-AzureADMSGroupLifecyclePolicy för att ange livs längden för alla Office 365-grupper i Azure AD-organisationen till 365 dagar. Förnyelse meddelanden för Office 365-grupper utan att ägare skickas tillemailaddress@contoso.com
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Hämta den befintliga principen get-AzureADMSGroupLifecyclePolicy: Den här cmdleten hämtar den aktuella Office 365-gruppens förfallo inställningar som har kon figurer ATS. I det här exemplet kan du se:

   - Princip-ID
   - Livs längden för alla Office 365-grupper i Azure AD-organisationen är inställd på 365 dagar
   - Förnyelse meddelanden för Office 365-grupper utan ägare skickas till "emailaddress@contoso.com."
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Uppdatera den befintliga princip uppsättningen-AzureADMSGroupLifecyclePolicy: Den här cmdleten används för att uppdatera en befintlig princip. I exemplet nedan ändras gruppens livs längd i den befintliga principen från 365 dagar till 180 dagar.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Lägga till vissa grupper i principen Add-AzureADMSLifecyclePolicyGroup: Den här cmdleten lägger till en grupp i livs cykel principen. Som exempel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Ta bort den befintliga principen Remove-AzureADMSGroupLifecyclePolicy: Den här cmdleten tar bort gruppens förfallo inställningar för Office 365 men kräver princip-ID. Detta kommer att inaktivera förfallo datum för Office 365-grupper.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Följande cmdletar kan användas för att konfigurera principen i mer detalj. Mer information finns i [PowerShell-dokumentationen](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om Azure AD-grupper.

- [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
