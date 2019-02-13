---
title: Skapa en dynamisk grupp och kontrollera status - Azure Active Directory | Microsoft Docs
description: Hur du skapar en gruppmedlemskap regler i Azure-portalen, kontrollera status.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5651d5e37613abcef8c8f5448af38637f91ebe30
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56193636"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Skapa en dynamisk grupp och kontrollera status

I Azure Active Directory (AD Azure), kan du skapa grupper genom att tillämpa en regel för att identifiera medlemskap baserat på användar- eller egenskaper. När attribut för en användare eller enhet ändrar, Azure AD utvärderar alla dynamiska regler i Azure AD-klient och utför någon lägger till eller tar bort. Om en användare eller enhet uppfyller en regel för en grupp, läggs de som en medlem och när de inte längre uppfyller regeln de tas bort.

Den här artikeln beskriver hur du konfigurerar en regel i Azure-portalen för dynamiskt medlemskap för säkerhetsgrupper eller Office 365-grupper. Exempel på regelsyntax och en fullständig lista över egenskaper som stöds, operatorer och värden för en medlemskapsregel finns i [regler för dynamiskt medlemskap för grupper i Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Skapa en regel för medlemskap

1. Logga in på den [Azure AD administratörscenter](https://aad.portal.azure.com) med ett konto som har rollen Global administratör, Intune Service-administratör eller Användaradministratör för kontot i klienten.
2. Välj **grupper**.
3. Välj **alla grupper**, och välj **ny grupp**.

   ![Lägg till ny grupp](./media/groups-create-rule/new-group-creation.png)

4. På den **grupp** bladet anger du ett namn och beskrivning för den nya gruppen. Välj en **Medlemskapstyp** antingen **dynamisk användare** eller **dynamisk enhet**, beroende på om du vill skapa en regel för användare eller enheter och välj sedan **Lägg till dynamisk fråga**. Du kan använda regeln builder för att skapa en enkel regel eller skriva en medlemskapsregel själv. Den här artikeln innehåller mer information om tillgängliga attribut för användare och enhet samt exempel på regler för medlemskap.

   ![Lägg till dynamisk medlemsregel](./media/groups-create-rule/add-dynamic-group-rule.png)

5. Om du vill se en fullständig lista över anpassade tilläggsegenskaper som du kan lägga till din medlemskapsfråga **hämta anpassade tilläggsegenskaper**, ange program-ID och välj sedan **uppdatera egenskaper för**. Fullständig ist egenskaper kommer nu att kan väljas.
6. När du har skapat regeln, Välj **Lägg till fråga** längst ned på bladet.
7. Välj **skapa** på den **grupp** bladet för att skapa gruppen.

> [!TIP]
> Det går inte att skapa en grupp om regeln som du angav har felaktigt format eller är inte giltig. Ett meddelande visas i det övre högra hörnet i portalen, som innehåller en förklaring av varför regeln inte kunde bearbetas. Läs den noggrant för att förstå hur du behöva justera regeln så att den blir giltigt.

## <a name="check-processing-status-for-a-membership-rule"></a>Kontrollera Bearbetningsstatus för för en medlemskapsregel

Du kan se medlemskapet bearbetning status och datum för senaste uppdatering på den **översikt** för gruppen.
  
  ![skärm för dynamisk grupp](./media/groups-create-rule/group-status.png)

Följande statusmeddelanden kan visas för **medlemskap bearbetning** status:

* **Utvärdera**:  Gruppändringen har tagits emot och uppdateringarna utvärderas.
* **Bearbetning av**: Uppdateringar som bearbetas.
* **Uppdateringen är klar**: Bearbetningen har slutförts och alla tillämpliga uppdateringar har gjorts.
* **Fel vid bearbetning av**: Ett fel uppstod vid utvärdering av medlemskapsregel och bearbetning kunde inte slutföras.
* **Uppdatera pausats**: Dynamiskt medlemskap har regel pausats av administratören. MembershipRuleProcessingState anges som ”pausad”.

Följande statusmeddelanden kan visas för **medlemskap senast uppdaterad** status:

* &lt;**Datum och tid**&gt;: Senast medlemskap har uppdaterats.
* **Pågår**: Uppdateringar pågår för tillfället.
* **Okänd**: Det går inte att hämta den senaste uppdateringstiden. Det kan bero på gruppen som nyligen skapas.

Om det uppstår ett fel vid bearbetning av medlemskapsregeln för en viss grupp, visas en avisering i den **översiktssidan** för gruppen. Om du inte väntar på dynamiskt medlemskap uppdateringar kan bearbetas för alla grupper i klienten för sedan 24 timmar, visas en avisering i **alla grupper**.

![felmeddelande för bearbetning](./media/groups-create-rule/processing-error.png)

Dessa artiklar innehåller ytterligare information om grupper i Azure Active Directory.

* [Visa befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Skapa en ny grupp och lägga till medlemmar](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
