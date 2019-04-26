---
title: Skapa en dynamisk grupp och kontrollera status - Azure Active Directory | Microsoft Docs
description: Hur du skapar en gruppmedlemskapsregel i Azure-portalen, kontrollera status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472178"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Skapa en dynamisk grupp och kontrollera status

I Azure Active Directory (AD Azure), kan du använda regler för att fastställa gruppmedlemskap baserat på användar- eller egenskaper. Den här artikeln beskriver hur du konfigurerar en regel för en dynamisk grupp i Azure-portalen.
Finns stöd för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper. När en gruppmedlemskapsregel används utvärderas användar- och attribut för matchningar med medlemskapsregeln för. När ett attribut ändras för en användare eller enhet, bearbetas alla dynamiska gruppregler i organisationen för ändringar i medlemskap. Användare och enheter läggs till eller tas bort om de uppfyller villkoren för en grupp.

Exempel på syntax, egenskaper som stöds, operatorer och värden för en medlemskapsregel finns [regler för dynamiskt medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Skapa en regel för medlemskap

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som är i Global administratör, Intune-administratör eller användarrollen administratör i klienten.
2. Välj **grupper**.
3. Välj **alla grupper**, och välj **ny grupp**.

   ![Välj kommandot för att lägga till ny grupp](./media/groups-create-rule/new-group-creation.png)

4. På den **grupp** anger du ett namn och beskrivning för den nya gruppen. Välj en **Medlemskapstyp** för användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Du kan använda regeln builder för att skapa en enkel regel eller [skriva en medlemskapsregel själv](groups-dynamic-membership.md).

   ![Lägg till regel för medlemskap för en dynamisk grupp](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Se anpassat tilläggs-egenskaper som är tillgängliga för din medlemskapsfråga
   1. Välj **hämta anpassat tilläggs-egenskaper**
   2. Ange program-ID och välj sedan **uppdatera egenskaper för**. 
6. När du har skapat regeln, Välj **Lägg till fråga** längst ned på bladet.
7. Välj **skapa** på den **grupp** bladet för att skapa gruppen.

Om regeln som du angav är inte giltig visas en förklaring av varför det gick inte att bearbeta regeln i det övre högra hörnet i portalen. Läs den noggrant för att förstå hur du åtgärdar regeln.

## <a name="turn-on-or-off-welcome-email"></a>Aktivera eller inaktivera e-post

När en ny Office 365-grupp skapas skickas en Välkommen meddelandet de användare som läggs till i gruppen. Senare, om ett attribut för en användare eller enhet ändrar, bearbetas alla dynamiska gruppregler i organisationen för ändringar i medlemskap. Användare som läggs får sedan även Välkommen meddelandet. Du kan inaktivera det här beteendet i [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps). 

## <a name="check-processing-status-for-a-rule"></a>Kontrollera Bearbetningsstatus för en regel för

Du kan se medlemskapet bearbetning status och datum för senaste uppdatering på den **översikt** för gruppen.
  
  ![visning av dynamisk grupp-status](./media/groups-create-rule/group-status.png)

Följande statusmeddelanden kan visas för **medlemskap bearbetning** status:

* **Utvärdera**:  Gruppändringen har tagits emot och uppdateringarna utvärderas.
* **Bearbetning av**: Uppdateringar som bearbetas.
* **Uppdateringen är klar**: Bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
* **Fel vid bearbetning av**:  Bearbetning kunde inte slutföras på grund av ett fel vid utvärdering av medlemskapsregel.
* **Uppdatera pausats**: Dynamiskt medlemskap har regel pausats av administratören. MembershipRuleProcessingState anges som ”pausad”.

Följande statusmeddelanden kan visas för **medlemskap senast uppdaterad** status:

* &lt;**Datum och tid**&gt;: Senast medlemskap har uppdaterats.
* **Pågår**: Uppdateringar pågår för tillfället.
* **Okänd**: Det går inte att hämta den senaste uppdateringstiden. Gruppen kan vara ny.

Om det uppstår ett fel vid bearbetning av medlemskapsregeln för en viss grupp, visas en avisering i den **översiktssidan** för gruppen. Om du inte väntar på dynamiskt medlemskap uppdateringar kan bearbetas för alla grupper i klienten för sedan 24 timmar, visas en avisering i **alla grupper**.

![bearbetning av felmeddelanden för meddelande](./media/groups-create-rule/processing-error.png)

Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
