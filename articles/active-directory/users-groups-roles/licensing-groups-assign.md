---
title: Tilldela licenser till en grupp – Azure Active Directory | Microsoft Docs
description: Hur du tilldelar licenser till användare med hjälp av Azure Active Directory-grupp licensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 02/25/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 423399c93fca222dad2db3175036719a0cd5fdb9
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986166"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Tilldela licenser till användare av gruppmedlemskap i Azure Active Directory

Den här artikeln beskriver hur du tilldelar licenser till en grupp med användare i Azure Active Directory (Azure AD) och verifiera att de är korrekt licensierade.

I det här exemplet innehåller klienten en säkerhetsgrupp med namnet **personalavdelningen**. Den här gruppen innehåller alla medlemmar i personalavdelningen (cirka 1 000 användare). Du vill tilldela Office 365 Enterprise E3-licenser till hela avdelningen. Yammer Enterprise-tjänsten som ingår i produkten måste inaktiveras tillfälligt tills avdelningen är redo att börja använda den. Du vill distribuera Enterprise Mobility + Security-licenser till samma grupp av användare.

> [!NOTE]
> Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, måste administratören ange egenskapen Usage plats på användaren.

> För gruppen licenstilldelning ärver alla användare utan att användningsplats angetts platsen för katalogen. Om du har användare på flera platser rekommenderar vi att du alltid ange användningsplats som en del av ditt användarflöde skapas i Azure AD (t.ex. via AAD Connect-konfiguration) – som säkerställer att resultatet av licenstilldelning alltid är korrekt och användarna får inte tjänster på platser som inte är tillåtna.

## <a name="step-1-assign-the-required-licenses"></a>Steg 1: Tilldela licenserna som krävs

1. Logga in på den [ **Azure AD administratörscenter** ](https://aad.portal.azure.com) licens med ett administratörskonto. För att hantera licenser, måste kontot vara en licensadministratör eller användare med rollen global administratör.

2. Välj **licenser** att öppna ett fönster där du kan visa och hantera alla licensierbara produkter i klienten.

4. Under **alla produkter**, Välj Office 365 Enterprise E5 och Enterprise Mobility + Security E3 genom att välja produktnamn. För att starta tilldelningen markerar **tilldela** överst i fönstret.

   ![Alla produkter, tilldela licens](./media/licensing-groups-assign/all-products-assign.png)
  
5. På den **tilldela licens** väljer **användare och grupper** att öppna en lista över användare och grupper.

6. Välj en användare eller grupp och sedan använda den **Välj** knappen längst ned i fönstret för att bekräfta valet.

7. På den **tilldela licens** fönstret klickar du på **tilldelningsalternativ**, som visar alla service-planer som ingår i de här två produkterna som vi har valt tidigare. Hitta **Yammer Enterprise** och aktivera den **av** att inaktivera tjänsten från produktlicensen. Bekräfta genom att klicka på **OK** längst ned i **licensalternativ**.

   ![Tilldelningsalternativ](./media/licensing-groups-assign/assignment-options.png)
  
8. Slutför tilldelningen genom att i fönstret **Tilldela licens** klicka på **Tilldela** längst ned i fönstret.

9. I det övre högra hörnet som visar status och resultatet av visas ett meddelande. Om tilldelning till gruppen inte gick att slutföra (till exempel på grund av befintliga licenser i gruppen), klickar du på meddelandet för att visa information om felet.

När tilldela licenser till en grupp, Azure AD bearbetar alla befintliga medlemmar i gruppen. Den här processen kan ta lite tid, varierande med storleken på gruppen. Nästa steg beskriver hur du kontrollerar att processen är klar och fastställa om ytterligare åtgärder krävs för att lösa problem.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Steg 2: Verifiera att den första tilldelningen är klar

1. Gå till **Azure Active Directory** > **grupper**. Välj den grupp som har tilldelats licenser.

2. I den grupp i rutan väljer **licenser**. På så sätt kan du snabbt bekräfta om licenser har tilldelats fullständigt till användare och om det finns några fel som du behöver titta i. Följande information är tillgänglig:

   - Lista över produktlicenser som för närvarande är kopplade till gruppen. Markera en post för att visa de specifika tjänster som har aktiverats och göra ändringar.

   - Status för senaste licensändringar som har gjorts i gruppen (om ändringarna bearbetas eller om bearbetningen har slutförts för alla användarmedlemmar).

   - Information om användare som ingår i ett feltillstånd eftersom inte det gick att tilldela licenser till dem.

   ![Tilldelningsalternativ](./media/licensing-groups-assign/assignment-errors.png)

3. Visa mer detaljerad information om licens bearbetning under **Azure Active Directory** > **användare och grupper** > *gruppnamn*  >  **Granskningsloggar**. Observera följande aktiviteter:

   - Aktivitet: **Börja tillämpa gruppbaserad licens för användare**. Detta loggas när systemet tar upp licenstilldelning ändringen i den aktuella och startar den tillämpas på alla användarmedlemmar. Den innehåller information om ändringen som gjordes.

   - Aktivitet: **Slutför tillämpning av gruppbaserad licens för användare**. Detta loggas när systemet har slutfört behandlingen alla användare i gruppen. Den innehåller en sammanfattning av hur många användare har bearbetats och hur många användare gick inte att koppla grupplicenserna.

   [Läs det här avsnittet](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) mer information om hur granskningsloggar kan användas för att analysera ändringar gjorda av gruppbaserad licensiering.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Steg 3: Sök efter licensproblem och löser dem.

1. Gå till **Azure Active Directory** > **grupper**, och hitta gruppen som har tilldelats licenser.
2. I den grupp i rutan väljer **licenser**. Meddelandet överst i fönstret visar att det finns 10 användare licenser kunde inte tilldelas till. Öppna den för att se en lista över alla användare i en licensiering feltillstånd i den här gruppen.
3. Den **misslyckade tilldelningar** kolumnen talar om för oss att båda produktlicenser inte kunde tilldelas till användare. Den **viktigaste orsaken till felet** kolumnen innehåller orsaken till felet. I det här fallet den har **pågår service-planer**.

   ![Misslyckade tilldelningar](./media/licensing-groups-assign/failed-assignments.png)

4. Välj en användare att öppna den **licenser** fönstret. Det här fönstret visar alla licenser som för närvarande är kopplade till användaren. I det här exemplet har du Office 365 Enterprise E1-licens som har ärvts från den **helskärmsläge användare** grupp. Detta är i konflikt med E3-licens som ett försök gjordes att tillämpa från den **personalavdelningen** grupp. Därmed att ingen av licenser från gruppen har tilldelats till användaren.

   ![Visa licenser för en användare](./media/licensing-groups-assign/user-license-view.png)

5. Lös konflikten genom att ta bort användaren från den **helskärmsläge användare** grupp. När Azure AD bearbetar ändringen, den **personalavdelningen** licenserna tilldelas korrekt.

   ![Korrekt licens](./media/licensing-groups-assign/license-correctly-assigned.png)

## <a name="next-steps"></a>Nästa steg

Mer information om funktionen för hantering av programvarulicenser via grupper finns i följande artiklar:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](../active-directory-licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
