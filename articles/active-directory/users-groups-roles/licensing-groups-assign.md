---
title: Tilldela licenser till en grupp - Azure Active Directory | Microsoft-dokument
description: Så här tilldelar du licenser till användare med hjälp av Azure Active Directory-grupplicensiering
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 130ce05e332f4705feb4acd54cbeb25d25a82532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253070"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Tilldela licenser till användare efter gruppmedlemskap i Azure Active Directory

I den här artikeln får du hjälp med att tilldela produktlicenser till en grupp användare och verifiera att de är licensierade korrekt i Azure Active Directory (Azure AD).

I det här exemplet innehåller klienten en säkerhetsgrupp som heter **HR-avdelningen**. I den här gruppen ingår alla medlemmar på personalavdelningen (cirka 1 000 användare). Du vill tilldela Office 365 Enterprise E3-licenser till hela avdelningen. Yammer Enterprise-tjänsten som ingår i produkten måste inaktiveras tillfälligt tills avdelningen är redo att börja använda den. Du vill också distribuera Enterprise Mobility + Security-licenser till samma grupp av användare.

> [!NOTE]
> Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en licens kan tilldelas en användare måste administratören ange egenskapen Användningsplats för användaren.
>
> För grupplicenstilldelning ärver alla användare utan angiven användningsplats platsen för katalogen. Om du har användare på flera platser rekommenderar vi att du alltid anger användningsplats som en del av ditt användarskapande flöde i Azure AD (t.ex. via AAD Connect-konfiguration) - som säkerställer att resultatet av licenstilldelning alltid är korrekt och att användarna inte får tjänster på platser som inte är tillåtna.

## <a name="step-1-assign-the-required-licenses"></a>Steg 1: Tilldela de licenser som krävs

1. Logga in på [**Azure AD-administrationscentret**](https://aad.portal.azure.com) med ett licensadministratörskonto. För att hantera licenser måste kontot vara en licensadministratör, användaradministratör eller global administratör.

1. Välj **Licenser** för att öppna en sida där du kan se och hantera alla licenspliktiga produkter i klienten.

1. Under **Alla produkter**väljer du både Office 365 Enterprise E5 och Enterprise Mobility + Security E3 genom att välja produktnamn. Om du vill starta tilldelningen väljer du **Tilldela** högst upp på sidan.

   ![Välj produkter som ska tilldela licenser](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. På sidan **Tilldela licens** väljer du Användare **och grupper** för att öppna en lista över användare och grupper.

1. Välj en användare eller grupp och använd sedan knappen **Välj** längst ned på sidan för att bekräfta ditt val.

1. På sidan **Tilldela licens** klickar du på **Tilldelningsalternativ**, som visar alla serviceplaner som ingår i de två produkter som vi valde tidigare. Hitta **Yammer Enterprise** och inaktivera **den** för att inaktivera den tjänsten från produktlicensen. Bekräfta genom att klicka på **OK** längst ned i **licensalternativ**.

   ![välj serviceplaner för licenser](./media/licensing-groups-assign/assignment-options.png)
  
1. Om du vill slutföra tilldelningen klickar du på **Tilldela** längst ned på sidan på sidan **Tilldela licens.**

1. Ett meddelande visas i det övre högra hörnet som visar processens status och resultat. Om tilldelningen till gruppen inte kunde slutföras (till exempel på grund av befintliga licenser i gruppen) klickar du på meddelandet för att visa information om felet.

När du tilldelar licenser till en grupp bearbetar Azure AD alla befintliga medlemmar i den gruppen. Den här processen kan ta lite tid, varierar med gruppens storlek. Nästa steg beskriver hur du kontrollerar att processen är klar och avgöra om ytterligare uppmärksamhet krävs för att lösa problem.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Steg 2: Kontrollera att den ursprungliga tilldelningen är klar

1. Gå till **Azure Active Directory** > **Groups**. Välj den grupp som licenser har tilldelats.

1. Välj **Licenser**på gruppsidan . På så sätt kan du snabbt bekräfta om licenser har tilldelats användarna helt och om det finns några fel som du behöver undersöka. Följande information finns tillgänglig:

   - Tjänstlicenser som för närvarande har tilldelats gruppen. Välj en post för att visa de specifika tjänster som har aktiverats och för att göra ändringar.

   - Statusuppdateringar av de senaste licensändringarna, som är tillgängliga om ändringarna bearbetas eller om bearbetningen är klar för alla användarmedlemmar.

   - Information om användarlicenstilldelningar som är i feltillstånd.

   ![licensfel och licensstatus](./media/licensing-groups-assign/assignment-errors.png)

1. Mer information om licensbearbetning under **Azure Active Directory** > **- användare och gruppnamn** > *group name* > **Granskningsloggar**. Kontrollera följande aktiviteter:

   - Aktivitet: `Start applying group based license to users`. Detta loggas när systemet hämtar ändringen av licenstilldelningen i gruppen och börjar tillämpa den på alla användarmedlemmar. Den innehåller information om den ändring som gjordes.

   - Aktivitet: `Finish applying group based license to users`. Detta loggas när systemet är klart bearbetning av alla användare i gruppen. Den innehåller en sammanfattning av hur många användare som har bearbetats och hur många användare som inte kunde tilldelas grupplicenser.

   [Läs det här avsnittet](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) om du vill veta mer om hur granskningsloggar kan användas för att analysera ändringar som görs av gruppbaserad licensiering.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Steg 3: Sök efter licensproblem och lösa dem

1. Gå till **Azure Active Directory** > **Groups**och hitta den grupp som licenser har tilldelats.
1. Välj **Licenser**på gruppsidan . Aviseringen överst på sidan visar att det finns 10 användare som licenser inte kunde tilldelas. Öppna den för att visa en lista över alla användare i ett licensieringsfeltillstånd för den här gruppen.
1. Kolumnen **Misslyckade tilldelningar** visar att båda produktlicenserna inte kunde tilldelas användarna. Den **översta orsaken till felkolumnen** innehåller orsaken till felet. I det här fallet är det **motstridiga serviceplaner**.

   ![licenser som inte kunde tilldelas](./media/licensing-groups-assign/failed-assignments.png)

1. Välj en användare om du vill öppna användarens **licenssida.** På den här sidan visas alla licenser som för närvarande har tilldelats användaren. I det här exemplet har användaren office 365 Enterprise E1-licensen som ärvdes från gruppen **Kiosk-användare.** Detta strider mot E3-licensen som systemet försökte tillämpa från **HR-avdelningens** grupp. Därför har ingen av licenserna från den gruppen tilldelats användaren.

   ![Visa alla licenskonflikter för en användare](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Lös den här konflikten genom att ta bort användaren från gruppen **Kiosk-användare.** När Azure AD har bearbetat ändringen tilldelas **HR-avdelningslicenserna** korrekt.

## <a name="next-steps"></a>Nästa steg

Mer information om funktionsuppsättningen för licenstilldelning med hjälp av grupper finns i följande artiklar:

- [Vad är gruppbaserad licensiering i Azure Active Directory?](/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
- [Migrera användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
- [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](../active-directory-licensing-group-advanced.md)
- [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
