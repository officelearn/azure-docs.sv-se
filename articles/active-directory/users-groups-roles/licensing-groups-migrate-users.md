---
title: Migrera användare licenser användare till gruppbaserad licensiering i Azure Active Directory | Microsoft Docs
description: Växla från enskilda användarlicenser till gruppbaserad licensiering med Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 10/29/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: b735d994b0a1937bd7bb3571aa1c642bca77817d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55182990"
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Hur du lägger till licensierade användare till en grupp för licensiering i Azure Active Directory

Du kan ha befintliga licenser som distribueras till användare i organisationer via ”direkttilldelning”; det vill säga med hjälp av PowerShell-skript eller andra verktyg för att tilldela användarlicenser för enskilda. Om du vill börja använda gruppbaserad licensiering för att hantera licenser i din organisation behöver en migreringsplan för att ersätta befintliga lösningar sömlöst med gruppbaserad licensiering.

Det viktigaste att tänka på är att du bör undvika att en situation där migrera till gruppbaserad licensiering leder användare tillfälligt förlorar sin tilldelade licenser. En process som kan leda till borttagning av licenser bör undvikas för att ta bort risken för användare att förlora åtkomsten till tjänster och deras data.

## <a name="recommended-migration-process"></a>Rekommenderade migreringsprocessen

1. Du har befintliga automation (till exempel PowerShell) som hanterar licenstilldelning och borttagning av användare. Låt den köra skick.

2. Skapa en ny licensiering grupp (eller bestämma vilka befintliga grupper som ska användas) och se till att alla nödvändiga användare läggs till som medlemmar.

3. Tilldela licenserna som krävs till dessa grupper. målet ska vara att återspegla den samma licensieringstillstånd ditt befintliga automation (till exempel PowerShell) tillämpas på dessa användare.

4. Kontrollera att licenser har tillämpats för alla användare i dessa grupper. Det här programmet kan göras genom att kontrollera Bearbetningsstatus för i varje grupp och genom att kontrollera granskningsloggar.

  - Du kan stickkontrollera enskilda användare genom att titta på sina licensinformationen. Du ser att de har samma licenser ”direkt” och ”ärvt” från grupper.

  - Du kan köra ett PowerShell.skript för att [kontrollera hur licenserna tilldelas till användare](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - Om samma produktlicensen har tilldelats användaren både direkt och via en grupp kan förbrukas endast en licens av användaren. Därför krävs inga ytterligare licenser för att utföra migrering.

5. Kontrollera att hitta några licensuppgifter misslyckades genom att kontrollera varje grupp för användare med feltillstånd. Mer information finns i [identifiera och lösa licensproblem för en grupp](licensing-groups-resolve-problems.md).

6. Överväg att ta bort de ursprungliga direkt tilldelningarna; Du kanske vill göra det gradvis, i ”vågor” att övervaka resultatet för en delmängd användare först.

  Du kan lämna de ursprungliga direkt tilldelningarna på användare, men när användarna lämnar sina licensierade grupper de, behåller den ursprungliga licensen, vilket är eventuellt inte vad du vill ha.

## <a name="an-example"></a>Ett exempel

En organisation har 1 000 användare. Alla användare kräver Enterprise Mobility + Security (EMS) licenser. 200 användare är på ekonomiavdelningen och kräver Office 365 Enterprise E3-licenser. Organisationen har för närvarande ett PowerShell-skript som körs lokalt, lägga till och ta bort licenser från användare när de kommer och går. Men vill organisationen ersätta skriptet med gruppbaserad licensiering så licenser kan hanteras automatiskt av Azure AD.

Här är hur migreringsprocessen kan se ut:

1. Med Azure portal, tilldela EMS-licens till den **alla användare** i Azure AD. Tilldela E3-licens till den **ekonomiavdelningen** grupp som innehåller alla användare.

2. Bekräfta att licenstilldelning har slutförts för alla användare för varje grupp. Gå till bladet för varje grupp, Välj **licenser**, och kontrollera bearbetningsstatusen överst i den **licenser** bladet.

  - Se ut för ”senaste licensen ändringar har tillämpats för alla användare” för att bekräfta bearbetningen har slutförts.

  - Leta efter ett meddelande längst upp om alla användare för vilka licenser inte kan har tilldelats. Kör vi inga fler licenser för vissa användare? Har några användare i konflikt licens-SKU: er som hindrar dem från ärver grupplicenserna?

3. Plats kontrollera vissa användare för att kontrollera att de har både direkt och gruppen licenserna tillämpas. Gå till bladet för en användare, väljer **licenser**, och undersök tillståndet för licenser.

  - Det här är det förväntade användartillståndet under migreringen:

      ![förväntade användartillstånd](./media/licensing-groups-migrate-users/expected-user-state.png)

  Detta bekräftar att användaren har både direkt och ärvda licenser. Ser vi att båda **EMS** och **E3** tilldelas.

  - Välj varje licens att visa information om aktiverade tjänster. Detta kan användas för att kontrollera om direct och gruppen licenser aktiverar exakt de samma service-planerna för användaren.

      ![Kontrollera service-planer](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Du kan börja ta bort direkt licenser från användare när du har bekräftat att både direkt och gruppen licenser är likvärdiga. Du kan testa detta genom att ta bort dem för enskilda användare i portalen och sedan köra automatiserade skript om du vill ha dem bort gruppvis. Här är ett exempel på samma användare med direkt licenser tas bort via portalen. Observera att licensen förblir oförändrat, men vi inte längre visas direkt tilldelningar.

  ![Direct licenser har tagits bort](./media/licensing-groups-migrate-users/direct-licenses-removed.png)


## <a name="next-steps"></a>Nästa steg

Mer information om övriga scenarier för hantering av programvarulicenser via grupper

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
