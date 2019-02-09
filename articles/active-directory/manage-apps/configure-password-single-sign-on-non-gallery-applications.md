---
title: Så här konfigurerar du lösenord för enkel inloggning för en inte är ett galleriprogram applicationn | Microsoft Docs
description: Hur du konfigurerar ett anpassad icke-galleriprogram för säker lösenordsbaserad enkel inloggning när det inte visas i Azure AD-Programgalleriet
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: celested
ms.openlocfilehash: e9b0cc8c1268cd5772e515cc923a3455763c1374
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961031"
---
# <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för en icke-galleriprogram

Förutom de alternativ som finns i Azure AD-Programgalleriet har du också möjlighet att lägga till en **icke-galleriprogram** när du vill att programmet inte finns. Den här funktionen kan du kan lägga till alla program som redan finns i din organisation eller något från tredje part-program som du kan använda från en leverantör som inte är redan en del av den [Azure AD-Programgalleriet](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

När du lägger till en icke-galleriprogram, du kan sedan konfigurera enkel inloggning för metoden det här programmet används genom att välja den **enkel inloggning** navigering objekt i ett Enterprise-program i den [Azure-portalen](https://portal.azure.com/).

En av enkel inloggning metoder tillgängliga för dig är det [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) alternativet. Med den **lägga till en icke-galleriprogram** upplevelse, kan du integrera alla program som återger ett HTML-baserad användarnamn och lösenord anger fältet, även om den inte ingår i vår uppsättning redan integrerade program.

Hur detta fungerar är av en sida som skrapning teknik som ingår i åtkomstpanelen-tillägg som ger oss möjlighet att automatiskt identifiera inmatningsfält för användarnamn och lösenord, lagra dem på ett säkert sätt för din specifika programinstans. På ett säkert sätt spela upp användarnamn och lösenord till dessa fält när en användare navigerar till programmet på programåtkomstpanelen.

Detta är ett bra sätt att komma igång med att integrera alla typer av program i Azure AD snabbt och kan du:

-   Integrera **alla program i hela världen** med Azure AD-klienten, så länge som det visas med ett HTML-användarnamn och lösenord inmatningsfält

-   Aktivera **enkel inloggning för användarnas** genom att på ett säkert sätt lagra och spela upp användarnamn och lösenord för programmet som du har integrerat med Azure AD

-   **Automatisk identifiering av indata** fält för alla program och gör att du kan identifiera dessa fält med hjälp av den Programåtkomstpanelens webbläsartillägg, manuellt om automatisk identifiering inte hitta dem.

-   **Stöd för program som kräver flera inloggningsfält** för program som kräver mer än bara användarnamn och lösenord fält att logga in

-   **Anpassa etiketterna** användarnamn och lösenord indatafält användarna ser på den [Programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Tillåt dina **användare** att ange användarnamn och lösenord för alla befintliga program-konton som de skriver i manuellt på den [programmets åtkomstpanel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Tillåt en **medlem i gruppen företag** att ange användarnamn och lösenord som har tilldelats till en användare med hjälp av den [Självbetjäning för programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funktion

-   Tillåt en **administratör** att ange användarnamn och lösenord som har tilldelats en användare med hjälp av funktionen Update autentiseringsuppgifter när du tilldelar en användare till ett program

-   Tillåt en **administratör** ange delade användarnamnet eller lösenordet som används av en grupp människor med hjälp av uppdatera autentiseringsuppgifterna funktionen när [tilldela en grupp till ett program](#assign-an-application-to-a-group-directly)

I följande avsnitt beskrivs hur du kan aktivera [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) för program som du lägger till med hjälp av den **lägga till en icke-galleriprogram** upplevelse.

## <a name="overview-of-steps-required"></a>Översikt över steg som krävs

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägg till en icke-galleriprogram](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

-   Tilldela programmet till en användare eller grupp

    -   [Tilldela en användare till ett program direkt](#assign-a-user-to-an-application-directly)

    -   [Tilldela ett program till en grupp direkt](#assign-an-application-to-a-group-directly)

## <a name="add-a-non-gallery-application"></a>Lägg till en icke-galleriprogram

Följ dessa steg om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram**.

7.  Ange namnet på ditt program i den **namn** textrutan. Välj **lägga till.**

Du att kunna se programmets konfigurationsruta efter en kort period.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**. Det här är URL: en där användare anger sina användarnamn och lösenord för inloggning. Kontrollera fälten inloggning syns i URL: en.

10. Tilldela användare till programmet.

11. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.


## <a name="assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Öppna den **Lägg till tilldelning** fönstret klickar du på den **Lägg till** knappen ovanpå den **användare och grupper** lista.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

## <a name="assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Öppna den **Lägg till tilldelning** fönstret klickar du på den **Lägg till** knappen ovanpå den **användare och grupper** lista.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till mer än en grupp**, typ i en annan **fullständiga namn** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
