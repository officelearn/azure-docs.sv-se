---
title: Så här konfigurerar du lösenord för enkel inloggning för ett program för Azure AD-galleriet | Microsoft Docs
description: Hur du konfigurerar ett program för säker lösenordsbaserad enkel inloggning när det finns redan i Azure AD Application Gallery
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
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 22a7a4e25a3df1e9f50b9b3038d4a6bd90ecd408
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335082"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för Azure AD-galleriet

När du lägger till ett program från den [Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery), du kan välja mellan hur du vill att användarna att logga in på programmet. Du kan konfigurera detta val när som helst genom att välja den **enkel inloggning** navigeringsobjektet på en Enterprise-programmet i den [Azure-portalen](https://portal.azure.com/).

En av enkel inloggning metoder tillgängliga för dig är det [lösenordsbaserade enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) alternativet. Detta är ett bra sätt att komma igång snabbt integrera program i Azure AD och kan du:

-   Aktivera **enkel inloggning för användarnas** genom att lagra och spela upp användarnamn och lösenord för programmet på ett säkert sätt som du har integrerat med Azure AD

-   **Stöd för program som kräver flera inloggning fält** för program som kräver mer än bara fälten användarnamn och lösenord för inloggning

-   **Anpassa etiketter** användarnamn och lösenord indatafält användarna ser på den [programmet åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Tillåt din **användare** att ange användarnamn och lösenord för alla program-konton som de skriver manuellt på den [åtkomstpanelen för programmet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Tillåt en **medlem i gruppen företag** att ange användarnamn och lösenord som tilldelats till en användare med hjälp av den [Self-Service programåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) funktion

-   Tillåt en **administratör** för att ange användarnamn och lösenord som tilldelats till en användare med hjälp av referenser uppdatering funktionen när [tilldela en användare till ett program](#assign-a-user-to-an-application-directly)

-   Tillåt en **administratör** ange delade användarnamnet eller lösenordet som används av en grupp människor med hjälp av autentiseringsuppgifter för Update funktionen när [tilldela en grupp till ett program](#assign-an-application-to-a-group-directly)

I följande avsnitt beskrivs hur du kan aktivera [lösenordsbaserade enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) till ett program som redan finns i den [Azure AD Application Gallery](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#get-started-with-the-azure-ad-application-gallery).

## <a name="overview-of-steps-required"></a>Översikt över steg som krävs
Så här konfigurerar du ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

-   [Tilldela program till en användare eller grupp](#assign-the-application-to-a-user-or-a-group)

    -   [Tilldela en användare till ett program direkt](#assign-a-user-to-an-application-directly)

    -   [Tilldela ett program till en grupp direkt](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ anvisningarna nedan om du vill lägga till ett program från Azure AD-galleriet

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** fönstret.

6.  I den **anger du ett namn** textruta från den **Lägg till från galleriet** avsnittet, skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textruta.

9.  Klicka på **Lägg till** för att lägga till programmet.

Du kan se programmets konfiguration fönstret efter en kort period.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  [Tilldela användare till programmet](#assign-a-user-to-an-application-directly).

10. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

## <a name="assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmenyn vänstra navigeringsfönstret.

8.  Klickar du på den **Lägg till** knappen ovanpå det **användare och grupper** att öppna den **Lägg uppdrag** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** ruta för att välja en roll för att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

## <a name="assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmenyn vänstra navigeringsfönstret.

8.  Klickar du på den **Lägg till** knappen ovanpå det **användare och grupper** att öppna den **Lägg uppdrag** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** fönstret.

10. Ange den **fullständig gruppnamn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klickar du på kryssrutan bredvid gruppen profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en grupp**, typ i en annan **fullständig gruppnamn** till den **Sök efter namn eller e-postadress** sökrutan och klickar du på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** ruta för att välja en roll som ska tilldelas de grupper som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda grupperna.

Användare som du har valt att kunna starta programmen på åtkomstpanelen efter en kort period.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
