---
title: Tilldela användare och grupper till ett program | Microsoft Docs
description: Tilldela användare till programmet för att bevilja åtkomst
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: d357a9a7f249127289a256685d9555f777742b68
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44357471"
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Tilldela användare och grupper till ett program

Innan användarna kan göra något av följande för ett visst program, måste du först **tilldela dem till programmet** att ge åtkomst:

-   Få åtkomst till ett program genom att **navigera till programmets URL direkt** (även kallat SP-initierat inloggning).

-   Få åtkomst till ett program med hjälp av den **URL för användaråtkomst** på ett programs **egenskaper** sidan (även kallat IDP-initierad inloggning).

-   Finns i ett program som visas på deras [Programåtkomstpanelen](https://myapps.microsoft.com/) eller mobila program.

-   Finns i ett program som visas på deras [startprogrammet för Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Metoder för att tilldela program med Azure Active Directory 

Det finns 3 sätt som du kan tilldela program med Azure Active Directory:

-   [Tilldela en användare direkt till ett program som administratör](#assign-a-user-directly-as-an-administrator)

-   [Tilldela en grupp direkt till ett program som administratör](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Tilldela en användare direkt som en administratör

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Tilldela en grupp direkt till ett program som administratör

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till mer än en grupp**, typ i en annan **fullständiga namn** till den **Sök efter namn eller e-postadress** sökrutan och Klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

Användare i de grupper som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod. Om dessa är dynamiska grupper kan finnas det vissa ytterligare bearbetning-fördröjning i dessa tilldelningar visas för användare inom dessa tilldelade grupper.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Aktivera självbetjäning programåtkomst så att användarna kan hitta sina egna program

Självbetjäning för programåtkomst är ett bra sätt så att användarna kan identifiera lokal program, låta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som har tilldelats till de användarna av lösenord för enkel inloggning på program direkt från sina åtkomstpaneler.

Följ stegen nedan om du vill aktivera självbetjäningsprogram åtkomst till ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill ge åtkomst till i listan.

7.  När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8.  Om du vill aktivera självbetjäningsåtkomsten för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9.  Klicka sedan på väljaren bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **till vilken grupp ska tilldelade användare läggas?** och välj en grupp.

10. **Valfritt:** om du inte vill att kräva ett företag godkännande innan användarna får åtkomst genom att ange den **kräver godkännande innan du beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: för program som använder lösenord för enkel inloggning på endast** om du vill att dessa företagsgodkännaren att ange de lösenord som skickas till det här programmet för godkända användare kan ställa in den **godkännare ha tillåtelse att ange användarens lösenord för det här programmet?**  växla till **Ja**.

12. **Valfritt:** för att ange den företagsgodkännaren som har behörighet att bevilja åtkomst till det här programmet, klickar du på väljaren bredvid etiketten **som har behörighet att bevilja åtkomst till det här programmet?** att välja upp till 10 person företagsgodkännaren.

  >[!NOTE]
  >Grupper stöds inte.
  >
  >

13. **Valfritt:** **för program som exponera roller**, om du vill tilldela självbetjäning godkända användare till en roll, klickar du på väljaren bredvid den **till vilken roll ska användarna tilldelas i det här programmet?** att välja rollen som dessa användare ska tilldelas.

14. Klicka på den **spara** längst upp i fönstret för att slutföra.

När du har slutfört konfigurationen av programåtkomst via självbetjäning kan användare navigera till sina [Programåtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företagsgodkännaren får även ett meddelande i sina [Programåtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder enkel godkännandearbetsflöden, vilket innebär att om du anger flera godkännare kan en enda godkännare kan godkännaren åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
