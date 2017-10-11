---
title: Tilldela licenser till en grupp i Azure Active Directory | Microsoft Docs
description: "Tilldela licenser till användare med hjälp av Azure Active Directory-grupp licensiering"
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/05/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 42b18eab9cb419e6ada72ba72dc8be8d7f7b2eed
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Tilldela licenser till användare av medlemskap i Azure Active Directory

Den här artikeln beskriver hur du tilldelar licenser till en grupp användare i Azure Active Directory (Azure AD) och verifiera att de är korrekt licensierade.

I det här exemplet innehåller klienten en säkerhetsgrupp med namnet **personalavdelningen**. Den här gruppen innehåller alla medlemmar i personalavdelningen (cirka 1 000 användare). Du vill tilldela licenser för Office 365 Enterprise E3 till hela avdelningen. Tjänsten Yammer Enterprise som ingår i produkten måste inaktiveras tillfälligt tills avdelningen är redo att börja använda den. Du bör också distribuera Enterprise Mobility + Security licenser till samma grupp av användare.

> [!NOTE]
> Vissa Microsoft-tjänster är inte tillgängliga på alla platser. Innan en användare kan tilldelas en licens, måste administratören ange egenskapen Usage plats på användaren.

> För gruppen licenstilldelning ärver alla användare utan en användningsplats anges platsen för katalogen. Om du har användare på flera platser rekommenderar vi att du alltid anger användningsplats som en del av din användare skapa flödet i Azure AD (t.ex. via AAD Connect-konfiguration) – som innebär att resultatet av licenstilldelning alltid är korrekt inte och användarna får tjänster på platser som inte tillåts.

## <a name="step-1-assign-the-required-licenses"></a>Steg 1: Tilldela licenser som krävs

1. Logga in på den [ **Azure-portalen** ](https://portal.azure.com) med ett administratörskonto. Kontot måste vara en global administratör roll eller användargrupp kontoadministratör om du vill hantera licenser.

2. Välj **fler tjänster** i det vänstra navigeringsfönstret och välj sedan **Azure Active Directory**. Du kan lägga till det här bladet Favoriter eller Fäst på portalens instrumentpanel.

3. På den **Azure Active Directory** bladet väljer **licenser**. Då öppnas ett blad där du kan se och hantera alla licensierbart produkter i klienten.

4. Under **alla produkter**, Välj Office 365 Enterprise E3 och Enterprise Mobility + Security genom att välja produktnamn. Om du vill starta tilldelningen, Välj **tilldela** längst upp på bladet.

   ![Alla produkter, tilldela en licens](media/active-directory-licensing-group-assignment-azure-portal/all-products-assign.png)

5. På den **tilldela licens** bladet, klickar du på **användare och grupper** att öppna den **användare och grupper** bladet. Sök efter namnet på *personalavdelningen*gruppen och välj sedan måste du kontrollera genom att klicka på **Välj** längst ned på bladet.

   ![Välj en grupp](media/active-directory-licensing-group-assignment-azure-portal/select-a-group.png)

6. På den **tilldela licens** bladet, klickar du på **tilldelning alternativ (valfritt)**, som visar alla serviceplaner som ingår i de här två produkterna som vi valt tidigare. Hitta **Yammer Enterprise** och stänga den **av** att inaktivera tjänsten från produktlicensen. Bekräfta genom att klicka på **OK** längst ned i **tilldelning alternativ**.

   ![Alternativ för tilldelning](media/active-directory-licensing-group-assignment-azure-portal/assignment-options.png)

7. Att slutföra en aktivitet, på den **tilldela licens** bladet, klickar du på **tilldela** längst ned på bladet.

8. Ett meddelande visas i det övre högra hörnet som visar status och resultatet av processen. Om tilldelning till gruppen inte gick att slutföra (t.ex, på grund av befintliga licenser i gruppen), klickar du på meddelandet för att visa information om felet.

Nu har vi angett en licensmall för personalavdelningen gruppen. Bakgrunden i Azure AD har startats för att bearbeta alla befintliga medlemmar i gruppen. Inledande åtgärden kan ta en stund, beroende på den aktuella storleken i gruppen. I nästa steg ska vi beskrivs hur du kontrollerar att processen är klar och avgöra om ytterligare åtgärder krävs för att lösa problem.

> [!NOTE]
> Du kan starta samma tilldelning från en annan plats: **användare och grupper** i Azure AD. Gå till **Azure Active Directory** > **användare och grupper** > **alla grupper**. Hitta gruppen markerar du den och gå till den **licenser** fliken. Den **tilldela** knappen ovanpå bladet öppnar bladet licens tilldelning.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Steg 2: Verifiera att den ursprungliga tilldelningen är klar

1. Gå till **Azure Active Directory** > **användare och grupper** > **alla grupper**. Leta reda på den **personalavdelningen** grupp som har tilldelats licenser.

2. På den **personalavdelningen** grupp bladet väljer **licenser**. På så sätt kan du snabbt bekräfta om licenser fullständigt har tilldelats till användare och om det finns några fel som du behöver titta i. Följande information är tillgänglig:

   - Lista över produktlicenser som för närvarande har tilldelats gruppen. Markera en post för att visa tjänsterna som har aktiverats och göra ändringar.

   - Status för de senaste licens ändringarna som gjorts i gruppen (ändringarna bearbetas eller om bearbetningen har slutförts för alla medlemmar i användare).

   - Information om användare som är i ett feltillstånd eftersom inte gick att tilldela licenser till dem.

   ![Alternativ för tilldelning](media/active-directory-licensing-group-assignment-azure-portal/assignment-errors.png)

3. Se mer detaljerad information om licens bearbetning under **Azure Active Directory** > **användare och grupper** > *gruppnamn*  >  **Granskningsloggar**. Observera följande aktiviteter:

   - Aktiviteten: **starta tillämpas licens grupp baserat på användare**. Detta loggas när systemet hämtar licenstilldelning ändringen i gruppen och börjar tillämpas på alla medlemmar av användaren. Innehåller information om ändringen som gjordes.

   - Aktiviteten: **Slutför tillämpas licens grupp baserat på användare**. Detta loggas när systemet har avslutat bearbetningen alla användare i gruppen. Den innehåller en sammanfattning av hur många användare bearbetades och hur många användare gick inte att tilldela licenser för gruppen.

   [Det här avsnittet](./active-directory-licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) lära dig mer om hur granskningsloggar kan användas för att analysera ändringar som gjorts av gruppbaserade licensiering.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Steg 3: Kontrollera problem med licensen och löser dem.

1. Gå till **Azure Active Directory** > **användare och grupper** > **alla grupper**, och Sök efter den **personalavdelningen** grupp som har tilldelats licenser.
2. På den **personalavdelningen** grupp bladet väljer **licenser**. Meddelandet ovanpå bladet visar att det finns 10 användare inte gick att tilldela licenser till. Om du klickar på den öppnas en lista över alla användare i en licensiering feltillstånd för den här gruppen.
3. Den **misslyckades tilldelningar** kolumnen talar om för oss att båda produktlicenser inte gick att koppla till användarna. Den **de främsta orsaken till felet** kolumnen innehåller orsaken till felet. I det här fallet har **i konflikt serviceplaner**.

   ![Misslyckade tilldelningar](media/active-directory-licensing-group-assignment-azure-portal/failed-assignments.png)

4. Välj en användare att öppna den **licenser** bladet. Det här bladet visar alla licenser som för närvarande tilldelats användaren. I det här exemplet användaren har den licens som Office 365 Enterprise E1 som har ärvts från det **helskärmsläge användare** grupp. Detta står i konflikt med E3-licens som ett försök gjordes att tillämpa från den **personalavdelningen** grupp. Därför kan har inga licenser från gruppen tilldelats användaren.

   ![Visa licenser för en användare](media/active-directory-licensing-group-assignment-azure-portal/user-license-view.png)

5. Du kan lösa konflikten genom att ta bort användaren från den **helskärmsläge användare** grupp. När Azure AD bearbetar ändras, den **personalavdelningen** licenser tilldelas korrekt.

   ![Korrekt licens](media/active-directory-licensing-group-assignment-azure-portal/license-correctly-assigned.png)

## <a name="next-steps"></a>Nästa steg

Mer information om funktionen för hantering av programvarulicenser genom grupper finns i följande artiklar:

* [Vad är gruppbaserade licensiering i Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identifiera och lösa eventuella problem med licens för en grupp i Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Hur du migrerar enskilda licensierade användare till gruppbaserade licensiering i Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory gruppbaserade licensiering fler scenarier](active-directory-licensing-group-advanced.md)
