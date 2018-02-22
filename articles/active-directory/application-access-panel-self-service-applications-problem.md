---
title: "Problemet med hjälp av självbetjäning programåtkomst | Microsoft Docs"
description: "Felsöka problem som rör självbetjäning programåtkomst"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 6d4044414cfae9a79487d02709aab24998fdef0b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="problem-using-self-service-application-access"></a>Problemet med hjälp av självbetjäning programåtkomst

Självbetjäning programåtkomst är ett bra sätt att tillåta användarna att identifiera program, automatisk låta affärsgruppen att godkänna åtkomst till dessa program. Du kan tillåta affärsgruppen att hantera de autentiseringsuppgifter som tilldelats till användare för höger lösenord enkel inloggning på program från deras åtkomst paneler.

Innan användarna kan själva identifiera program från deras åtkomstpanelen, måste du aktivera **självbetjäning programåtkomst** för program som du vill tillåta användare att identifiera själv och begära åtkomst till.

## <a name="general-issues-to-check-first"></a>Allmänna problem med att kontrollera först

-   Kontrollera att självbetjäning programåtkomst är korrekt konfigurerad. Se ”hur du konfigurerar självbetjäning programåtkomst”.

-   Kontrollera att användaren eller gruppen har aktiverats för att begära självbetjäning programåtkomst.

-   Kontrollera att användaren besöker på rätt plats för självbetjäning programåtkomst. användare kan navigera till deras [programmet åtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst.

-   Om självbetjäning programåtkomst konfigurerades har nyligen, försök att logga in och ut igen i användarens åtkomstpanelen efter några minuter att se om ändringarna självbetjäning åtkomst har visats.

## <a name="how-to-configure-self-service-application-access"></a>Konfigurera självbetjäning programåtkomst

Följ stegen nedan om du vill aktivera självbetjäning programmet åtkomst till ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill aktivera självbetjäning åtkomst till i listan.

7.  När programmet läses in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8.  Om du vill aktivera självbetjäning programåtkomst för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9.  Klicka sedan på selector bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **vilken grupp ska tilldelade användare läggas?** och välja en grupp.

10. **Valfritt:** om du vill kräva en business godkännande innan användare tillåts åtkomst genom att ange den **kräver godkännande innan åtkomst beviljas till det här programmet?** växla till **Ja**.

11. **Valfritt: för program som använder enkel inloggning för lösenord på endast** om du vill att dessa företag godkännare att ange de lösenord som skickas till det här programmet för godkända användare måste ange den **Tillåt godkännare att ange användarens lösenord för det här programmet?**  växla till **Ja**.

12. **Valfritt:** om du vill ange godkännare för företag som har behörighet att godkänna åtkomst till det här programmet, klickar du på väljaren bredvid etiketten **som har tillåtelse att godkänna åtkomst till det här programmet?** att välja upp till 10 person företag godkännare.

 >[!NOTE]
 > Grupper stöds inte.
 >
 >

13. **Valfritt:** **för program som exponera roller**, om du vill tilldela en roll godkända Självbetjäningsanvändare, klickar du på väljaren bredvid den **vilken roll ska användare tilldelas i det här programmet?** att välja rollen som användarna ska tilldelas.

14. Klicka på den **spara** längst upp på bladet för att avsluta.

När du har slutfört självbetjäning programkonfigurationen användare kan navigera till deras [programmet åtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företag godkännare också se ett meddelande i sina [programmet åtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande till dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöd för godkännande endast arbetsflöden, vilket innebär att om du anger flera godkännare alla godkännare godkänna åtkomst till programmet.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Om felsökningen inte löser problemet 

Öppna ett supportärende med följande information om de är tillgängliga:

-   Fel-ID för korrelation

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsperioden under fel inträffar

-   Fiddler spårningar

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](active-directory-accessmanagement-self-service-group-management.md)
