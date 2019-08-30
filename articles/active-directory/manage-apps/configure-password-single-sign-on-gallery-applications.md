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
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146894"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

När du lägger till ett program från [Azure Active Directory (Azure AD)-program galleriet](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)kan du välja hur du vill att användarna ska logga in i programmet. Du kan konfigurera det här alternativet när du vill genom att välja **enkel inloggning** på ett företags program i [Azure Portal](https://portal.azure.com/).

Ett av de tillgängliga alternativen för enkel inloggning (SSO) är [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis). Det här är ett bra sätt att börja snabbt integrera program i Azure AD. Det möjliggör följande:

-   Lagrar och spelar upp användar namn och lösen ord på ett säkert sätt för det program som du har integrerat med Azure AD

-   Ger stöd för program som kräver flera inloggnings fält utöver bara användar namn och lösen ords fält

-   Gör att du kan anpassa etiketterna för fälten användar namn och lösen ord som användarna ser på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Gör att användarna kan ange sina egna användar namn och lösen ord för alla befintliga program konton som de anger manuellt på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Låter en medlem i affärs gruppen använda funktionen för självbetjänings [program åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) för att ange användar namn och lösen ord som tilldelats en användare

-   Gör det möjligt för en administratör att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen uppdatera autentiseringsuppgifter när de [tilldelar en användare till ett program](#assign-a-user-to-an-application-directly)

-   Låter en administratör använda funktionen uppdatera autentiseringsuppgifter för att ange det delade användar namnet eller lösen ordet för en grupp personer när de [tilldelar en grupp till ett program](#assign-an-application-to-a-group-directly)

I följande avsnitt beskrivs hur du kan aktivera [lösenordsbaserad enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) till ett program som redan finns i [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)-programgalleriet.

## <a name="overview-of-required-steps"></a>Översikt över nödvändiga steg
Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

-   Tilldela programmet till en användare eller grupp:

    -   [Tilldela en användare till ett program direkt](#assign-a-user-to-an-application-directly)

    -   [Tilldela ett program till en grupp direkt](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ dessa steg om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com)och logga in som **Global administratör** eller medadministratör.

2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** längst upp på menyn till vänster.

3.  Ange **Azure Active Directory** i sökrutan och välj sedan **Azure Active Directory** objekt.

4.  Välj **företags program** på Azure AD-menyn till vänster.

5.  Välj knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** anger du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i rutan **namn** .

9.  Välj **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ dessa steg om du vill konfigurera enkel inloggning för ett program:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör** eller medadministratör.

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** längst upp på menyn till vänster.

3. Ange **Azure Active Directory** i sökrutan och välj sedan **Azure Active Directory** objekt.

4. Välj **företags program** på Azure Active Directory menyn till vänster.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   Om du inte ser det program som du vill använda, använder du **filter** kontrollen längst upp i **listan över program**och anger alternativet **Visa** för **alla program**.

6. Välj det program som du vill konfigurera för enkel inloggning.

7. När programmet har lästs in väljer du **enkel inloggning** från programmets meny till vänster.

8. Välj **lösenordsbaserad inloggnings** läge.

9. [Tilldela användare till programmet](#assign-a-user-to-an-application-directly).

10. Du kan också ange autentiseringsuppgifter för användares räkning genom att välja en användares rad, välja **uppdatera autentiseringsuppgifter**och sedan ange användar namn och lösen ord. Annars uppmanas användarna att ange sina autentiseringsuppgifter när de startar programmet.

## <a name="assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ dessa steg om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör**.

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** längst upp på menyn till vänster.

3. Ange **Azure Active Directory** i sökrutan och välj sedan **Azure Active Directory** objekt.

4. Välj **företags program** på Azure Active Directory menyn till vänster.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   Om du inte ser det program som du vill använda, använder du **filter** kontrollen längst upp i **listan över program**och anger alternativet **Visa** för **alla program**.

6. Välj det program som du vill tilldela en användare till.

7. När programmet har lästs in väljer **du användare och grupper** från programmets meny till vänster.

8. Välj knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Ange användarens fullständiga namn eller e-postadress i sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över användaren i listan och markera sedan kryss rutan bredvid användarens profil bild eller logo typ för att lägga till dem i den **markerade** listan.

12. Valfritt: Om du vill lägga till fler än en användare anger du ett annat fullständigt namn eller e-postadress i rutan **Sök efter namn eller e-postadress** och markerar kryss rutan för användaren för att lägga till dem i den **markerade** listan.

13. När du är klar med att välja användare använder du knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. Valfritt: Använd kommandot **Välj roll** i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de användare som du har valt.

15. Välj **tilldela** för att tilldela programmet till de valda användarna.

## <a name="assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ dessa steg om du vill tilldela en eller flera grupper till ett program direkt:

1. Öppna [Azure Portal](https://portal.azure.com/) och logga in som **Global administratör**.

2. Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** längst upp på menyn till vänster.

3. Ange **Azure Active Directory** i sökrutan och välj sedan **Azure Active Directory** objekt.

4. Välj **företags program** från Azure AD-menyn till vänster.

5. Välj **alla program** om du vill visa en lista över alla dina program.

   Om du inte ser det program som du vill använda, använder du **filter** kontrollen längst upp i **listan över program** och anger alternativet **Visa** för **alla program**.

6. Välj det program som du vill tilldela en användare till.

7. När programmet har lästs in väljer **du användare och grupper** från programmets meny till vänster.

8. Klicka på knappen **Lägg till** längst upp i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

9. Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

10. Ange det fullständiga grupp namnet för den grupp som du vill tilldela i sökrutan **Sök efter namn eller e-postadress** .

11. Hovra över **gruppen** i listan och markera sedan kryss rutan bredvid gruppens profil bild eller logo typ för att lägga till gruppen i den **markerade** listan.

12. Valfritt: Om du vill lägga till fler än en grupp anger du ett annat fullständigt grupp namn i sökrutan **Sök efter namn eller e-postadress** och markerar sedan motsvarande kryss ruta för att lägga till den här gruppen i den **markerade** listan.

13. När du är klar med att välja grupper använder du knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. Valfritt: Använd kommandot **Välj roll** i fönstret **Lägg till tilldelning** för att välja en roll som ska tilldelas de grupper som du har valt.

15. Välj **tilldela** för att tilldela programmet till de valda grupperna.

Efter en kort period ska de användare du har valt kunna starta programmen från åtkomst panelen.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar via Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).
