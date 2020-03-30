---
title: Lägga till användare med direktlicenser för grupplicensiering – Azure AD | Microsoft-dokument
description: Migrera från enskilda användarlicenser till gruppbaserad licensiering med Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c06d81f2f3f6cee781889d05ae08a1fd125df52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025685"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Migrera användare med enskilda licenser till grupper för licensiering

Du kan ha befintliga licenser som distribueras till användare i organisationerna via direkt tilldelning. det vill ha med PowerShell-skript eller andra verktyg för att tilldela enskilda användarlicenser. Innan du börjar använda gruppbaserad licensiering för att hantera licenser i organisationen kan du använda den här migreringsplanen för att sömlöst ersätta befintliga lösningar med gruppbaserad licensiering.

Det viktigaste att tänka på är att du bör undvika en situation där migrera till gruppbaserad licensiering kommer att resultera i att användare tillfälligt förlorar sina licenser som för närvarande tilldelats. Alla processer som kan leda till att licenser tas bort bör undvikas för att ta bort risken för att användare förlorar åtkomst till tjänster och deras data.

## <a name="recommended-migration-process"></a>Rekommenderad migreringsprocess

1. Du har befintlig automatisering (till exempel PowerShell) som hanterar licenstilldelning och borttagning för användare. Låt den vara igång som den är.

1. Skapa en ny licensieringsgrupp (eller bestäm vilka befintliga grupper som ska användas) och se till att alla nödvändiga användare läggs till som medlemmar.

1. Tilldela de licenser som krävs till dessa grupper. Ditt mål bör vara att återspegla samma licensieringstillstånd som din befintliga automatisering (till exempel PowerShell) gäller för dessa användare.

1. Kontrollera att licenser har tillämpats på alla användare i dessa grupper. Det här programmet kan göras genom att kontrollera bearbetningstillståndet för varje grupp och genom att kontrollera granskningsloggar.

   - Du kan upptäcka kontrollera enskilda användare genom att titta på deras licensuppgifter. Du kommer att se att de har samma licenser som tilldelats "direkt" och "ärvt" från grupper.

   - Du kan köra ett PowerShell-skript för att [verifiera hur licenser tilldelas användare](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - När samma produktlicens tilldelas användaren både direkt och via en grupp förbrukas endast en licens av användaren. Därför krävs inga ytterligare licenser för att utföra migreringen.

1. Kontrollera att inga licenstilldelningar misslyckades genom att kontrollera varje grupp efter användare i feltillstånd. Mer information finns i [Identifiera och lösa licensproblem för en grupp](licensing-groups-resolve-problems.md).

Överväg att ta bort de ursprungliga direkta tilldelningarna. Vi rekommenderar att du gör det gradvis och övervakar resultatet på en delmängd av användarna först. Om du kunde lämna de ursprungliga direkta tilldelningarna till användare, men när användarna lämnar sina licensierade grupper behåller de direkt tilldelade licenserna, vilket kanske inte är vad du vill.

## <a name="an-example"></a>Ett exempel

En organisation har 1 000 användare. Alla användare kräver Office 365 Enterprise E3-licenser. För närvarande har organisationen ett PowerShell-skript som körs lokalt och lägger till och tar bort licenser från användare när de kommer och går. Organisationen vill dock ersätta skriptet med gruppbaserad licensiering så att licenser kan hanteras automatiskt av Azure AD.

Så här kan migreringsprocessen se ut:

1. Med Hjälp av Azure-portalen tilldelar du Office 365 E3-licensen till gruppen **Alla användare** i Azure AD.

1. Bekräfta att licenstilldelningen har slutförts för alla användare. Gå till översiktssidan för gruppen, välj **Licenser**och kontrollera bearbetningsstatusen högst upp på **bladet Licenser.**

   - Leta efter "Senaste licensändringar har tillämpats på alla användare" för att bekräfta att bearbetningen har slutförts.

   - Leta efter ett meddelande ovanpå om alla användare för vilka licenser kanske inte har tilldelats. Har vi på licenser för vissa användare? Har vissa användare licensplaner i konflikt som hindrar dem från att ärva grupplicenser?

1. Avmarkera vissa användare för att kontrollera att de har både direkt- och grupplicenser tillämpade. Gå till profilsidan för en användare, välj **Licenser**och undersök tillståndet för licenser.

   - Det här är det förväntade användartillståndet under migreringen:

      ![förväntat användartillstånd under migreringen](./media/licensing-groups-migrate-users/expected-user-state.png)

     Detta bekräftar att användaren har både direkta och ärvda licenser. Vi ser att Office 365 E3 har tilldelats.

   - Välj varje licens för att se vilka tjänster som är aktiverade. Om du vill kontrollera att direkt- och grupplicenserna aktiverar exakt samma tjänster för användaren väljer du **Tilldelningar**.

1. När du har bekräftat att både direkt- och grupplicenser är likvärdiga kan du börja ta bort direkta licenser från användare. Du kan testa detta genom att ta bort dem för enskilda användare i portalen och sedan köra automatiseringsskript för att få dem borttagna i bulk. Här är ett exempel på samma användare med direkta licenser bort via portalen. Observera att licenstillståndet förblir oförändrat, men vi ser inte längre direkta tilldelningar.

   ![bekräfta att direkta licenser tas bort](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om andra scenarier för grupplicenshantering:

- [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
- [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
- [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
- [Migrera användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](licensing-groups-change-licenses.md)
- [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
- [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](licensing-ps-examples.md)
