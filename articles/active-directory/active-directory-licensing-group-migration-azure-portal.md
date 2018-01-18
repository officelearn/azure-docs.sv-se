---
title: "Migrera användare licenser användare till gruppbaserade licensiering i Azure Active Directory | Microsoft Docs"
description: "Växla från enskilda användarlicenser för gruppbaserade med Azure Active Directory"
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2018
ms.author: curtand
ms.custom: seohack1
ms.openlocfilehash: 47d7bf5a41c140c6d6769b549163a3dadfa5ef1c
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="how-to-add-licensed-users-to-a-group-for-licensing-in-azure-active-directory"></a>Hur du lägger till licensierade användare till en grupp för licensiering i Azure Active Directory

Du kan ha befintliga licenser som distribueras till användare i organisationer via ”direkttilldelning”; det vill säga med hjälp av PowerShell-skript eller andra verktyg för att tilldela användarlicenser för enskilda. Om du vill börja använda gruppbaserade licensiering för att hantera licenser i din organisation behöver en migreringsplan sömlöst ersätta befintliga lösningar med gruppbaserade licensiering.

Det viktigaste att tänka på är att du bör undvika att en situation där migrera till gruppbaserade licensiering leder till att användare tillfälligt förlorar sin aktuella tilldelade licenser. En process som kan leda till borttagning av licenser bör undvikas för att ta bort risken för användare att förlora åtkomsten till deras data och tjänster.

## <a name="recommended-migration-process"></a>Rekommenderade migreringsprocessen

1. Du har befintliga automation (till exempel PowerShell) hantera licenstilldelning och borttagning för användare. Låt den körs som är.

2. Skapa en ny grupp för licensiering (eller bestämma vilka befintliga grupper som ska användas) och se till att alla nödvändiga användare läggs till som medlemmar.

3. Tilldela licenser som krävs till dessa grupper. målet ska vara så att den samma licensiering statusen som tillämpar dina befintliga automation (till exempel PowerShell) för dessa användare.

4. Kontrollera att licenser har tillämpats för alla användare i dessa grupper. Detta kan göras genom att kontrollera tillståndet för bearbetning på varje grupp och genom att kontrollera granskningsloggar.

  - Du kan plats enskilda användare genom att titta på deras licensinformation. Du ser att de har samma användarlicenser ”direkt” och ”ärvt” från grupper.

  - Du kan köra ett PowerShell.skript för att [kontrollera hur licenser som tilldelats användare](active-directory-licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

  - När samma produktlicensen tilldelas användaren både direkt och via en grupp, används endast en licens av användaren. Därför krävs inga ytterligare licenser för att utföra migreringen.

5. Kontrollera att inga licenstilldelning inte genom att kontrollera varje grupp för användare i feltillstånd. Mer information finns i [identifiera och lösa problem med licens för en grupp](active-directory-licensing-group-problem-resolution-azure-portal.md).

6. Överväg att ta bort de ursprungliga direkt tilldelningarna; Du kanske vill göra det gradvis i ”waves” att övervaka resultatet för en delmängd användare först.

  Du kan också lämna ursprungliga direkt tilldelningar på användare, men när användarna lämnar deras licensierade grupper som de fortfarande behåller den ursprungliga licensen, vilket är eventuellt det du söker inte.

## <a name="an-example"></a>Ett exempel

Vi har en organisation med 1 000 användare. Alla användare kräver Enterprise Mobility + Security (EMS) licenser. 200 användare på ekonomiavdelningen och kräver Office 365 Enterprise E3 licenser. Vi har ett PowerShell-skript som körs på lokala att lägga till och ta bort licenser från användare eftersom de kommer och går. Vi vill ersätta skriptet med gruppbaserade licensing så licenser hanteras automatiskt av Azure AD.

Här är hur migreringsprocessen gick ut:

1. Med hjälp av Azure portal tilldela EMS-licens för att den **alla användare** i Azure AD. Tilldela E3-licens för att den **ekonomiavdelningen** grupp som innehåller alla användare.

2. Bekräfta att licenstilldelning har slutförts för alla användare för varje grupp. Gå till bladet för varje grupp markerar **licenser**, och kontrollera bearbetningsstatusen överst i den **licenser** bladet.

  - Sök efter ”senaste licens ändringarna har tillämpats för alla användare” för att bekräfta bearbetningen har slutförts.

  - Leta efter ett meddelande längst upp om någon användare för vilka licenser inte kan har tilldelats. Körde vi utanför licenser för vissa användare? Har några användare motstridiga licens-SKU: er som hindrar dem från arv grupp licenser?

3. Platsen kontrollera vissa användare för att kontrollera att de har både direkt och gruppen licenserna tillämpas. Gå till bladet för en användare väljer **licenser**, och undersök tillståndet för licenser.

  - Detta är förväntat användartillståndet under migreringen:

      ![förväntade användartillstånd](media/active-directory-licensing-group-migration-azure-portal/expected-user-state.png)

  Det här bekräftar att användaren har både direkt och ärvda licenser. Vi se att båda **EMS** och **E3** tilldelas.

  - Markera varje licens för att visa information om aktiverade tjänster. Detta kan användas för att kontrollera om licenser direkt och gruppen aktivera exakt samma serviceplaner för användaren.

      ![Kontrollera service-planer](media/active-directory-licensing-group-migration-azure-portal/check-service-plans.png)

4. När du har bekräftat att både direkt och gruppen licenser är likvärdiga, kan du börja ta bort direkt licenser från användare. Du kan testa detta genom att ta bort dem för enskilda användare i portalen och sedan köra automatiserade skript för att ta bort gruppvis. Här är ett exempel på samma användare med direkt licenser tas bort via portalen. Observera att licensen förblir oförändrat, men vi kan inte längre se direkt tilldelningar.

  ![direkt licenser tas bort](media/active-directory-licensing-group-migration-azure-portal/direct-licenses-removed.png)


## <a name="next-steps"></a>Nästa steg

Mer information om andra scenarier för hantering av programvarulicenser genom grupper

* [Tilldela licenser till en grupp i Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Vad är gruppbaserade licensiering i Azure Active Directory?](active-directory-licensing-whatis-azure-portal.md)
* [Identifiera och lösa eventuella problem med licens för en grupp i Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Azure Active Directory gruppbaserade licensiering fler scenarier](active-directory-licensing-group-advanced.md)
