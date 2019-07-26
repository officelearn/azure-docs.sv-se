---
title: Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program | Microsoft Docs
description: Konfigurera ett program för säker lösenordsbaserad enkel inloggning när det redan är listat i Azure AD-programgalleriet
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: eb37fe247901b799a845ce75723a4a6b535cbb28
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422578"
---
# <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

När du lägger till ett program från [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-programgalleriet kan du välja hur du vill att användarna ska logga in i programmet. Du kan konfigurera det här alternativet när du vill genom att välja navigerings objekt för **enkel inloggning** i ett företags program i [Azure Portal](https://portal.azure.com/).

En av de enkla inloggnings metoderna som är tillgängliga för dig är alternativet för [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) . Det här är ett bra sätt att komma igång med att integrera program i Azure AD snabbt och gör att du kan:

-   Aktivera **enkel inloggning för dina användare** genom säker lagring och uppspelning av användar namn och lösen ord för det program som du har integrerat med Azure AD

-   **Stöd för program som kräver flera inloggnings fält** för program som kräver mer än bara användar namn och lösen ords fält för att logga in

-   **Anpassa etiketterna** för fälten användar namn och lösen ord indatatyper som användarna ser på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Tillåt att **användarna** anger sina egna användar namn och lösen ord för alla befintliga program konton som de skriver in manuellt på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Tillåt en **medlem i företags gruppen** att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen för självbetjänings [åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Tillåt en **administratör** att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen uppdatera autentiseringsuppgifter när [en användare tilldelas till ett program](#assign-a-user-to-an-application-directly)

-   Tillåt en **administratör** att ange det delade användar namnet eller lösen ordet som används av en grupp med personer genom att använda funktionen uppdatera autentiseringsuppgifter när [en grupp tilldelas till ett program](#assign-an-application-to-a-group-directly)

I följande avsnitt beskrivs hur du kan aktivera [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) till ett program som redan finns i [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-programgalleriet.

## <a name="overview-of-steps-required"></a>Översikt över de steg som krävs
Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

-   Tilldela programmet till en användare eller grupp

    -   [Tilldela en användare till ett program direkt](#assign-a-user-to-an-application-directly)

    -   [Tilldela ett program till en grupp direkt](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller medadministratör

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på knappen **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör **.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. [Tilldela användare till programmet](#assign-a-user-to-an-application-directly).

10. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du in ett annat **fullständigt namn** eller **e-postadress** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

## <a name="assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständiga namn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid gruppen profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till fler än en grupp**skriver du in ett annat **fullständigt grupp namn** i sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till den här gruppen i den **markerade** listan.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll tilldelas till de grupper som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till valda grupper.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
