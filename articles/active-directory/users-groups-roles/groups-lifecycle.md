---
title: Ange förfallodatum för Office 365-grupper – Azure Active Directory | Microsoft-dokument
description: Konfigurera förfallodatum för Office 365-grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048250"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Konfigurera förfalloprincipen för Office 365-grupper

I den här artikeln beskrivs hur du hanterar livscykeln för Office 365-grupper genom att ange en förfalloprincip för dem. Du kan bara ange förfalloprincip för Office 365-grupper i Azure Active Directory (Azure AD).

När du har angett att en grupp ska upphöra att gälla:

- Grupper med användaraktiviteter förnyas automatiskt när förfallodatumet närmar sig.
- Ägare av gruppen meddelas att förnya gruppen, om gruppen inte förnyas automatiskt.
- Alla grupper som inte förnyas tas bort.
- Alla Office 365-grupper som tas bort kan återställas inom 30 dagar av gruppägarna eller administratören.

För närvarande kan endast en förfalloprincip konfigureras för alla Office 365-grupper i en Azure AD-organisation.

> [!NOTE]
> Konfigurera och använda principen för förfallodatum för Office 365-grupper kräver att du har men inte nödvändigtvis tilldela Azure AD Premium-licenser för medlemmarna i alla grupper som principen för förfallodatum tillämpas på.

Information om hur du hämtar och installerar Azure AD PowerShell-cmdletar finns i [Azure Active Directory PowerShell för Diagram 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Aktivitet baserad på automatisk förnyelse

Med Azure AD-intelligens förnyas grupper nu automatiskt baserat på om de nyligen har använts. Den här funktionen eliminerar behovet av manuella åtgärder från gruppägare, eftersom den baseras på användaraktivitet i grupper i Office 365-tjänster som Outlook, SharePoint eller Teams. Om till exempel en ägare eller gruppmedlem gör något som att ladda upp ett dokument i SharePoint, besöka en Teams-kanal eller skicka ett e-postmeddelande till gruppen i Outlook, förnyas gruppen automatiskt och ägaren får inga förnyelsemeddelanden.

### <a name="activities-that-automatically-renew-group-expiration"></a>Aktiviteter som automatiskt förnyar gruppens förfallodatum

Följande användaråtgärder orsakar automatisk gruppförnyelse:

- SharePoint: Visa, redigera, ladda ned, flytta, dela eller ladda upp filer
- Outlook: Gå med i grupp, läs-/skrivgruppmeddelande från grupputrymme, Gilla ett meddelande (i Outlook Web Access)
- Lag: Besök en Teams-kanal

### <a name="auditing-and-reporting"></a>Revision och rapportering

Administratörer kan få en lista över automatiskt förnyade grupper från aktivitetsgranskningsloggarna i Azure AD.

![Automatisk förnyelse av grupper baserat på aktivitet](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Roller och behörigheter

Följande är roller som kan konfigurera och använda förfallodatum för Office 365-grupper i Azure AD.

Roll | Behörigheter
-------- | --------
Global administratör, gruppadministratör eller användaradministratör | Kan skapa, läsa, uppdatera eller ta bort principinställningarna för office 365-grupper<br>Kan förnya en Office 365-grupp
Användare | Kan förnya en Office 365-grupp som de äger<br>Kan återställa en Office 365-grupp som de äger<br>Kan läsa inställningarna för förfalloprincipen

Mer information om behörigheter för att återställa en borttagen grupp finns i [Återställa en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Öppna [Azure AD-administrationscentret](https://aad.portal.azure.com) med ett konto som är en global administratör i din Azure AD-organisation.

2. Välj **Grupper**och välj sedan **Förfallodatum** för att öppna förfalloinställningarna.
  
   ![Inställningar för förfallodatum för grupper](./media/groups-lifecycle/expiration-settings.png)

3. På sidan **Förfallodatum** kan du:

    - Ange gruppens livstid i dagar. Du kan välja ett av de förinställda värdena eller ett anpassat värde (bör vara 31 dagar eller mer).
    - Ange en e-postadress där förnyelse- och förfallodatummeddelanden ska skickas när en grupp inte har någon ägare.
    - Välj vilka Office 365-grupper som upphör att gälla. Du kan ange förfallodatum för:
      - **Alla** Office 365-grupper
      - En lista över **markerade** Office 365-grupper
      - **Ingen** för att begränsa förfallodatum för alla grupper
    - Spara inställningarna när du är klar genom att välja **Spara**.

> [!NOTE]
> - När du först ställer in förfallodatumet anges alla grupper som är äldre än utgångsdatumet till 35 dagar fram till förfallodatumet om inte gruppen förnyas automatiskt eller ägaren förnyar det.
> - När en dynamisk grupp tas bort och återställs ses den som en ny grupp och fylls i igen enligt regeln. Den här processen kan ta upp till 24 timmar.
> - Förfallodatum för grupper som används i Teams visas i flödet Teams Owners.

## <a name="email-notifications"></a>E-postmeddelanden

Om grupper inte förnyas automatiskt skickas e-postmeddelanden som den här till Office 365-gruppägarna 30 dagar, 15 dagar och 1 dag före gruppens utgång. Språket i e-postmeddelandet bestäms av gruppägarens önskade språk eller Azure AD-språkinställning. Om gruppägaren har definierat ett önskat språk, eller om flera ägare har samma önskade språk, används det språket. För alla andra fall används Azure AD-språkinställningen.

![E-postmeddelanden om förfallodatum](./media/groups-lifecycle/expiration-notification.png)

I e-postmeddelandet **förnya gruppmeddelande** kan gruppägare komma direkt åt gruppinformationssidan på [åtkomstpanelen](https://account.activedirectory.windowsazure.com/r#/applications). Där kan användarna få mer information om gruppen, till exempel dess beskrivning, när den senast förnyades, när den upphör att gälla och även möjligheten att förnya gruppen. Gruppinformationssidan innehåller nu även länkar till Office 365-gruppresurserna, så att gruppägaren enkelt kan visa innehållet och aktiviteten i sin grupp.

När en grupp upphör att gälla tas gruppen bort en dag efter utgångsdatumet. Ett e-postmeddelande som det här skickas till Office 365-gruppägarna och informerar dem om att office 365-gruppen upphör att gälla och att de tas bort efter det.

![Gruppborttagning av e-postmeddelanden](./media/groups-lifecycle/deletion-notification.png)

Gruppen kan återställas inom 30 dagar efter borttagningen genom att välja **Återställningsgrupp** eller med PowerShell-cmdletar, enligt beskrivningen i [Återställ en borttagen Office 365-grupp i Azure Active Directory](groups-restore-deleted.md). Observera att 30-dagars grupprestaureringsperioden inte är anpassningsbar.

Om gruppen som du återställer innehåller dokument, SharePoint-webbplatser eller andra beständiga objekt kan det ta upp till 24 timmar innan gruppen och dess innehåll återställs helt.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Hämta utgångsdatum för Office 365-gruppen

Förutom Åtkomstpanelen där användare kan visa gruppinformation, inklusive utgångsdatum och senaste förnyelsedatum, kan utgångsdatum för en Office 365-grupp hämtas från Microsoft Graph REST API Beta. expirationDateTime som gruppegenskap har aktiverats i Microsoft Graph Beta. Den kan hämtas med en GET-begäran. Mer information finns i [det här exemplet](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> För att hantera gruppmedlemskap på åtkomstpanelen måste "Begränsa åtkomst till grupper i åtkomstpanelen" anges till "Nej" i allmän inställning för Azure Active Directory-grupper.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Så här fungerar office 365-gruppens förfallodatum med en postlåda vid juridisk spärr

När en grupp upphör att gälla och tas bort, behålls gruppens data från appar som Planner, Sites eller Teams permanent, men grupppostlådan som är juridisk spärra behåll behålls och tas inte bort permanent. Administratören kan använda Exchange-cmdletar för att återställa postlådan för att hämta data.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Så här fungerar office 365-gruppens förfallodatum med bevarandeprincip

Bevarandeprincipen konfigureras via Säkerhets- och efterlevnadscenter. Om du har skapat en bevarandeprincip för Office 365-grupper, när en grupp upphör att gälla och tas bort, behålls gruppkonversationerna i grupppostlådan och filerna på gruppplatsen i kvarhållningsbehållaren för det specifika antal dagar som definierats i bevarandet Politik. Användarna ser inte gruppen eller dess innehåll efter utgångsdatum, men kan återställa webbplats- och postlådedata via e-identifiering.

## <a name="powershell-examples"></a>PowerShell-exempel

Här är några exempel på hur du kan använda PowerShell-cmdletar för att konfigurera förfalloinställningarna för Office 365-grupper i din Azure AD-organisation:

1. Installera PowerShell v2.0-modulen och logga in vid PowerShell-prompten:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Konfigurera förfallodatuminställningarna Använd cmdleten New-AzureADMSGroupLifecyclePolicy för att ange livstid för alla Office 365-grupper i Azure AD-organisationen till 365 dagar. Förnyelsemeddelanden för Office 365-grupper utanemailaddress@contoso.comägare skickas till ' '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Hämta den befintliga principen Get-AzureADMSGroupLifecyclePolicy: Den här cmdlet hämtar de aktuella office 365-gruppinställningarna som har konfigurerats. I det här exemplet kan du se:

   - Princip-ID:et
   - Livslängden för alla Office 365-grupper i Azure AD-organisationen är satt till 365 dagar
   - Förnyelsemeddelanden för Office 365-grupper utanemailaddress@contoso.comägare skickas till '.'
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Uppdatera den befintliga principen Set-AzureADMSGroupLifecyclePolicy: Den här cmdleten används för att uppdatera en befintlig princip. I exemplet nedan ändras gruppens livstid i den befintliga principen från 365 dagar till 180 dagar.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Lägg till specifika grupper i principtillägget-AzureADMSLifecyclePolicyGroup: Den här cmdleten lägger till en grupp i livscykelprincipen. Som exempel:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Ta bort befintliga principborttagning-AzureADMSGroupLifecyclePolicy: Den här cmdleten tar bort office 365-gruppens förfallodatuminställningar men kräver princip-ID. Den här cmdleten inaktiverar förfallodatum för Office 365-grupper.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Följande cmdlets kan användas för att konfigurera principen mer i detalj. Mer information finns i [PowerShell-dokumentation](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Hämta-AzureADMSGroupLifecyclePolicy
- Ny-AzureADMSGroupLifecyclePolicy
- Hämta-AzureADMSGroupLifecyclePolicy
- Ange-AzureADMSGroupLifecyclePolicy
- Ta bort AzureADMSGroupLifecyclePolicy
- Lägg till AzureADMSLifecyclePolicyGroup
- Ta bort AzureADMSLifecyclePolicyGroup
- Återställ-AzureADMSLifeCycleGroup
- Hämta AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure AD-grupper.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
