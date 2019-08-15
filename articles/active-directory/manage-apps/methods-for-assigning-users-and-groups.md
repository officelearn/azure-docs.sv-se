---
title: Tilldela användare och grupper till ett program | Microsoft Docs
description: Tilldela användare till programmet för att bevilja åtkomst
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13c9003baaca11dce5a2192a8183674faddfa6dc
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967286"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Tilldela användare och grupper till ett program i Azure Active Directory
Den här artikeln visar hur du tilldelar användare eller grupper till ett program i Azure Active Directory (AD Azure). Användare måste först tilldelas till ett program innan en administratör kan ge dem åtkomst för att göra följande:

-   Få åtkomst till ett program genom att **navigera till programmets URL direkt** (även kallat SP-initierat inloggning).

-   Få åtkomst till ett program med hjälp av den **URL för användaråtkomst** på ett programs **egenskaper** sidan (även kallat IDP-initierad inloggning).

-   Finns i ett program som visas på deras [Programåtkomstpanelen](https://myapps.microsoft.com/) eller mobila program.

-   Finns i ett program som visas på deras [startprogrammet för Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Tillgängligheten för gruppbaserad tilldelning bestäms av ditt licens avtal. Gruppbaserad tilldelning stöds endast för säkerhets grupper. Kapslade grupp medlemskap och O365-grupper stöds inte för närvarande.

## <a name="prerequisites"></a>Förutsättningar
Innan du kan tilldela användare och grupper till ett program, måste du kräva Användartilldelning. Att kräva Användartilldelning:

1. Logga in på Azure-portalen med ett administratörskonto.
2. Klicka på den **alla tjänster** objekt på huvudmenyn.
3. Välj det bibliotek som du använder för programmet.
4. Klicka på den **företagsprogram** fliken.
5. Välj programmet i listan över program som är associerade med den här katalogen.
6. Klicka på den **egenskaper** fliken.
7. Ändra den **Användartilldelning krävs?** växla till Ja.
8. Klicka på den **spara** längst upp på skärmen.

## <a name="assign-users"></a>Tilldela användare

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

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

Efter en kort tids period kommer de användare du har valt att kunna starta programmen med de metoder som beskrivs i avsnittet lösnings beskrivning.

## <a name="assign-groups"></a>Tilldela grupper

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

12. **Valfritt:** Om du vill **lägga till fler än en grupp**skriver du in ett annat **fullständigt grupp namn** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till den här gruppen i den **markerade** listan.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

Efter en kort tids period kommer användare i de grupper du har valt att kunna starta programmen med hjälp av de metoder som beskrivs i avsnittet lösnings beskrivning. Om dessa är dynamiska grupper kan finnas det vissa ytterligare bearbetning-fördröjning i dessa tilldelningar visas för användare inom dessa tilldelade grupper.

## <a name="enable-self-service-application-access"></a>Aktivera Självbetjäning för programåtkomst

Självbetjäning för programåtkomst är ett bra sätt så att användarna kan identifiera lokal program, låta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som har tilldelats till de användarna av lösenord för enkel inloggning på program direkt från sina åtkomstpaneler.

Följ stegen nedan om du vill aktivera självbetjäningsprogram åtkomst till ett program:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från Azure Active Directory-vänstra navigeringsmenyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill ge åtkomst till i listan.

7. När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigeringsmenyn.

8. Om du vill aktivera självbetjäningsåtkomsten för det här programmet, aktivera den **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9. Klicka sedan på väljaren bredvid etiketten för att välja gruppen till vilken användare som begär åtkomst till det här programmet ska läggas till, **till vilken grupp ska tilldelade användare läggas?** och välj en grupp.

10. **Valfritt:** Om du vill kräva ett affärs godkännande innan användarna tillåts måste du ställa in **Kräv godkännande innan jag beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: För program som bara använder lösen ord för enkel inloggning** , om du vill att dessa affärs god kännare ska kunna ange lösen ord som skickas till det här programmet för godkända användare, ställer du in **Tillåt god kännare att ange användarens lösen ord för det här program?** Växla till **Ja**.

12. **Valfritt:** Om du vill ange de affärs god kännare som tillåts att godkänna åtkomst till det här programmet klickar du på väljaren bredvid den etikett **som har behörighet att godkänna åtkomst till det här programmet?** om du vill välja upp till 10 enskilda affärs god kännare.

    >[!NOTE]
    >Grupper stöds inte.
    >
    >

13. **Valfritt:** **För program som visar roller**, om du vill tilldela självbetjänings godkända användare till en roll, klickar du på väljaren bredvid **rollen till vilken roll ska användare tilldelas i det här programmet?** Välj den roll som användarna ska tilldelas till.

14. Klicka på den **spara** längst upp i fönstret för att slutföra.

När du har slutfört konfigurationen av programåtkomst via självbetjäning kan användare navigera till sina [Programåtkomstpanelen](https://myapps.microsoft.com/) och klicka på den **+ Lägg till** för att hitta de appar som du har aktiverat självbetjäning åtkomst. Företagsgodkännaren får även ett meddelande i sina [Programåtkomstpanelen](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder endast enskilda godkännande arbets flöden, vilket innebär att om du anger flera god kännare kan en enskild god kännare godkänna åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
