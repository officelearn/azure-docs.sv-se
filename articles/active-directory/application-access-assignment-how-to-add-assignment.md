---
title: "Tilldela användare och grupper till ett program | Microsoft Docs"
description: "Tilldela användare till programmet för att bevilja åtkomst"
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
ms.openlocfilehash: 61536612e0dd5102b8f5e911c350826846f5ed77
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Tilldela användare och grupper till ett program

Innan användarna kan göra något av de nedan för ett visst program måste du första **tilldela dem till programmet** att ge åtkomst:

-   Åtkomst till ett program genom **gå direkt till programmets URL** (även kallat SP-initierad inloggning).

-   Åtkomst till ett program med hjälp av den **användaren åtkomst-URL** på ett program **egenskaper** sida (även kallat IDP-initierad inloggning).

-   Se ett program som visas på sina [programmet åtkomstpanelen](https://myapps.microsoft.com/) eller mobila program.

-   Se ett program som visas på sina [startprogrammet för Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Metoder för att tilldela program med Azure Active Directory 

Det finns 3 sätt som du kan tilldela program med Azure Active Directory:

-   [Tilldela en användare direkt till ett program som administratör](#assign-a-user-directly-as-an-administrator)

-   [Tilldela en grupp direkt till ett program som administratör](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som en administratör

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

Användare som du har valt att kunna starta dessa program med hjälp av de metoder som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständig gruppnamn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klickar du på kryssrutan bredvid gruppen profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en grupp**, typ i en annan **fullständig gruppnamn** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll som ska tilldelas de grupper som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda grupperna.

Användare i de grupper som du har valt att kunna starta dessa program med hjälp av de metoder som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod. Om dessa är dynamiska grupper, kan det finnas vissa ytterligare bearbetning fördröjning i dessa tilldelningar visas för användare i de tilldelade grupper.

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

  >[!NOTE]
  >Grupper stöds inte.
  >
  >

13. **Valfritt:** **för program som exponera roller**, om du vill tilldela en roll godkända Självbetjäningsanvändare, klickar du på väljaren bredvid den **vilken roll ska användare tilldelas i det här programmet?** att välja rollen som användarna ska tilldelas.

14. Klicka på den **spara** längst upp på bladet för att avsluta.

När du har slutfört självbetjäning programkonfigurationen användare kan navigera till deras [programmet åtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företag godkännare också se ett meddelande i sina [programmet åtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande till dem när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder godkännandearbetsflöden, vilket innebär att om du anger flera godkännare alla godkännare kan godkännare åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
