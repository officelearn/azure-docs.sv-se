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
ms.openlocfilehash: abd6b13dc56f8f948d50e2b3564712ed8f5b1476
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376430"
---
# <a name="assign-users-and-groups-to-an-application-in-azure-active-directory"></a>Tilldela användare och grupper till ett program i Azure Active Directory
Den här artikeln visar hur du tilldelar användare eller grupper till ett program i Azure Active Directory (AD Azure). Användare måste först tilldelas till ett program innan en administratör kan ge dem åtkomst för att göra följande:

-   Öppna ett program genom **att gå till programmets URL direkt** (kallas även för SP-initierad inloggning).

-   Få åtkomst till ett program med hjälp av **URL: en för användar åtkomst** på programmets **egenskaps** sida (kallas även IDP-initierad inloggning).

-   Se ett program som visas på sin [program åtkomst panel](https://myapps.microsoft.com/) eller i ett mobil program.

-   Se ett program som visas i [Office 365-program](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

Tillgängligheten för gruppbaserad tilldelning bestäms av ditt licens avtal. Gruppbaserad tilldelning stöds endast för säkerhets grupper. Kapslade grupp medlemskap och O365-grupper stöds inte för närvarande.

## <a name="configure-the-application-to-require-assignment"></a>Konfigurera programmet så att det kräver tilldelning

Ett program kan konfigureras att kräva tilldelning innan det går att komma åt dem. Så här kräver du tilldelning:

1. Logga in på Azure Portal med ett administratörs konto eller som ägare till appen under **företags program**.
2. Klicka på objektet **alla tjänster** på huvud menyn.
3. Välj det bibliotek som du använder för programmet.
4. Klicka på fliken **företags program** .
5. Välj programmet i listan över program som är associerade med den här katalogen.
6. Klicka på fliken **Egenskaper** .
7. Vill du ändra **användar tilldelningen krävs?** växla till Ja.
8. Klicka på knappen **Spara** längst upp på skärmen.

## <a name="assign-users"></a>Tilldela användare

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör eller som en program ägare som inte är administratör.**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** i Azure Active Directory vänster hand navigerings meny.

5.  Klicka på **alla program** om du vill visa en lista över alla dina program.

    * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** i programmets vänstra navigerings meny.

8.  Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9.  Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga namnet** eller **e-postadressen** för den användare som du är intresse rad av att tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **användaren** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid användarens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du är färdig med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Efter en kort tids period kommer de användare du har valt att kunna starta programmen med de metoder som beskrivs i avsnittet lösnings beskrivning.

## <a name="assign-groups"></a>Tilldela grupper

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller som en icke-administratörs program ägare med en Azure AD Premium licens tilldelad.

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Klicka på **företags program** i Azure Active Directory vänster hand navigerings meny.

5.  Klicka på **alla program** om du vill visa en lista över alla dina program.

    * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** i programmets vänstra navigerings meny.

8.  Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9.  Klicka på Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Skriv in det **fullständiga grupp namnet** för den grupp som du vill tilldela till sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **gruppen** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid gruppens profil bild eller logo typ för att lägga till din användare i den **markerade** listan.

12. **Valfritt:** Om du vill **lägga till fler än en grupp**skriver du in ett annat **fullständigt grupp namn** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till den här gruppen i den **markerade** listan.

13. När du är färdig med att välja grupper klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll som du vill tilldela till de grupper som du har valt.

15. Klicka på knappen **tilldela** för att tilldela programmet till de valda grupperna.

Efter en kort tids period kommer användare i de grupper du har valt att kunna starta programmen med hjälp av de metoder som beskrivs i avsnittet lösnings beskrivning. Om dessa är dynamiska grupper kan finnas det vissa ytterligare bearbetning-fördröjning i dessa tilldelningar visas för användare inom dessa tilldelade grupper.

## <a name="enable-self-service-application-access"></a>Aktivera Självbetjäning för programåtkomst

Självbetjäning för programåtkomst är ett bra sätt så att användarna kan identifiera lokal program, låta affärsgrupp att godkänna åtkomst till dessa program. Du kan tillåta affärsgrupp att hantera de autentiseringsuppgifter som har tilldelats till de användarna av lösenord för enkel inloggning på program direkt från sina åtkomstpaneler.

Följ stegen nedan om du vill aktivera självbetjäningsprogram åtkomst till ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4. Klicka på **företags program** i Azure Active Directory vänster hand navigerings meny.

5. Klicka på **alla program** om du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du vill ge åtkomst till i listan.

7. När programmet har lästs in klickar du på **självbetjäning** från programmets vänstra navigerings meny.

8. Aktivera självbetjäning för program åtkomst för det här programmet genom att aktivera alternativet **Tillåt användare att begära åtkomst till det här programmet?** växla till **Ja.**

9. Välj sedan den grupp som användare som begär åtkomst till det här programmet ska läggas till genom att klicka på väljaren bredvid etiketten som **gruppen ska tilldelas användare till?** och välja en grupp.

10. **Valfritt:** Om du vill kräva ett affärs godkännande innan användarna tillåts måste du ställa in **Kräv godkännande innan jag beviljar åtkomst till det här programmet?** växla till **Ja**.

11. **Valfritt: endast för program som använder enkel inloggning med lösen ord,** om du vill tillåta att affärs god kännare anger lösen ord som skickas till det här programmet för godkända användare ställer du in alternativet **Tillåt god kännare att ange användarens lösen ord för det här programmet?** växla till **Ja**.

12. **Valfritt:** Om du vill ange de affärs god kännare som tillåts att godkänna åtkomst till det här programmet klickar du på väljaren bredvid den etikett **som har behörighet att godkänna åtkomst till det här programmet?** om du vill välja upp till 10 enskilda affärs god kännare.

    >[!NOTE]
    >Grupper stöds inte.
    >
    >

13. **Valfritt:** **för program som visar roller**, om du vill tilldela självbetjänings godkända användare till en roll, klickar du på väljaren bredvid **rollen till vilken roll ska användare tilldelas i det här programmet?** Välj den roll som användarna ska tilldelas till.

14. Klicka på knappen **Spara** längst upp i fönstret för att avsluta.

När du har slutfört självbetjänings program konfigurationen kan användarna navigera till sin [program åtkomst panel](https://myapps.microsoft.com/) och klicka på knappen **+ Lägg** till för att hitta appar som du har aktiverat självbetjänings åtkomst till. Affärs god kännare ser också ett meddelande i sin [program åtkomst panel](https://myapps.microsoft.com/). Du kan aktivera ett e-postmeddelande om när en användare har begärt åtkomst till ett program som kräver godkännande. 

Dessa godkännanden stöder endast enskilda godkännande arbets flöden, vilket innebär att om du anger flera god kännare kan en enskild god kännare godkänna åtkomst till programmet.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
