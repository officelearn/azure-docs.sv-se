---
title: "Hur du använder självbetjäning programåtkomst | Microsoft Docs"
description: "Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.reviewer: japere
ms.openlocfilehash: 08a05a70d976104d4e0a37b0a0dd15042b0212d8
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-use-self-service-application-access"></a>Hur du använder självbetjäning programåtkomst

Innan användarna kan själva identifiera program från deras åtkomstpanelen, måste du aktivera **självbetjäning programåtkomst** för program som du vill tillåta användare att identifiera själv och begära åtkomst till.

Den här funktionen är ett bra sätt att spara tid och pengar som IT-grupp och rekommenderas som en del av en distribution för moderna program med Azure Active Directory.

Den här funktionen kan du:

-   Användarna själva identifiera program från den [programmet åtkomstpanelen](https://myapps.microsoft.com/) utan stör IT-grupp.

-   Lägga till användare i en förkonfigurerad så att du kan se vem som har begärt åtkomst, ta bort åtkomst och hantera roller som tilldelats.

-   Alternativt kan en business godkännare godkänna programförfrågningar så att IT-grupp inte behöver.

-   Du kan också konfigurera upp till 10 personer som kan godkänna åtkomst till det här programmet.

-   Om du vill tillåta ett företag godkännare och ange sedan lösenorden dessa användare kan använda för att logga in på programmet, direkt från godkännaren företag [programmet åtkomstpanelen](https://myapps.microsoft.com/).

-   Du kan också automatiskt tilldela självbetjäning tilldelade användare till en roll för programmet direkt.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program

Självbetjäning programåtkomst är ett bra sätt att tillåta användarna att identifiera program, automatisk låta affärsgruppen att godkänna åtkomst till dessa program. Du kan tillåta affärsgruppen att hantera de autentiseringsuppgifter som tilldelats till användare för höger lösenord enkel inloggning på program från deras åtkomst paneler.

Följ stegen nedan om du vill aktivera självbetjäning programmet åtkomst till ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill aktivera självbetjäning åtkomst till i listan.

7.  När programmet läses in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8.  Om du vill aktivera självbetjäning programåtkomst för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9.  Klicka sedan på selector bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **vilken grupp ska tilldelade användare läggas?** och välja en grupp.

10. **Valfritt:** om du vill kräva en business godkännande innan användare tillåts åtkomst genom att ange den **kräver godkännande innan åtkomst beviljas till det här programmet?** växla till **Ja**.

11. **Valfritt: för program som använder enkel inloggning för lösenord på endast** om du vill att dessa företag godkännare att ange de lösenord som skickas till det här programmet för godkända användare måste ange den **Tillåt godkännare att ange användarens lösenord för det här programmet?** växla till **Ja**.

12. **Valfritt:** om du vill ange godkännare för företag som har behörighet att godkänna åtkomst till det här programmet, klickar du på väljaren bredvid etiketten **som har tillåtelse att godkänna åtkomst till det här programmet?** att välja upp till 10 enskilda företag godkännare.

   * Grupper stöds inte.

13. **Valfritt:** **för program som exponera roller**, om du vill tilldela en roll godkända Självbetjäningsanvändare, klickar du på väljaren bredvid den **vilken roll ska användare tilldelas i det här programmet?** att välja rollen som användarna ska tilldelas.

14. Klicka på den **spara** längst upp på bladet för att avsluta.

När du har slutfört självbetjäning programkonfigurationen användare kan navigera till deras [programmet åtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företag godkännare också se ett meddelande i sina [programmet åtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande till dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöd för godkännande endast arbetsflöden, vilket innebär att om du anger flera godkännare alla godkännare godkänna åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Azure Active Directory för grupphantering via självbetjäning](active-directory-accessmanagement-self-service-group-management.md)
