---
title: Konfigurera lösenordsbaserad inloggning för Azure AD-appar | Microsoft-dokument
description: Konfigurera lösenord enkel inloggning (SSO) till dina Azure AD-företagsprogram i Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 563bda275b73f76b042b5e57a9909ca78c504bb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063534"
---
# <a name="configure-password-single-sign-on"></a>Konfigurera enkel inloggning för lösenord

När du [lägger till en galleriapp](add-gallery-app.md) eller en [webbapp som inte är galleri](add-non-gallery-app.md) i dina Azure AD Enterprise-program är ett av de alternativ för enkel inloggning som är tillgängliga för dig [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md#password-based-sso). Det här alternativet är tillgängligt för alla webbplatser med en HTML-inloggningssida. Lösenordsbaserad SSO, även kallad lösenordsvalv, gör att du kan hantera användaråtkomst och lösenord till webbprogram som inte stöder identitetsfederation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, till exempel till organisationens konton för sociala medier. 

Lösenordsbaserad SSO är ett bra sätt att snabbt komma igång med att integrera program i Azure AD och gör att du kan:

-   Aktivera **enkel inloggning för dina användare** genom att säkert lagra och spela upp användarnamn och lösenord för det program som du har integrerat med Azure AD

-   **Stödprogram som kräver flera inloggningsfält** för program som kräver mer än bara användarnamn och lösenordsfält för att logga in

-   **Anpassa etiketterna för** de inmatningsfält för användarnamn och lösenord som användarna ser på [programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Tillåt **användarna** att ange sina egna användarnamn och lösenord för alla befintliga programkonton som de skriver in manuellt på [programåtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Tillåt en **medlem i företagsgruppen** att ange de användarnamn och lösenord som tilldelats en användare med hjälp av funktionen [Självbetjäningsprogramåtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Tillåt en **administratör** att ange ett användarnamn och lösenord som ska användas av personer eller grupper när du loggar in på programmet med hjälp av funktionen Uppdatera autentiseringsuppgifter 

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure [AD-klient](add-non-gallery-app.md)läser du Lägga till en [galleriapp](add-gallery-app.md) eller Lägg till en app som inte är galleri.

## <a name="open-the-app-and-select-password-single-sign-on"></a>Öppna appen och välj lösenord enkel inloggning

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

2. Navigera till **Azure Active Directory** > **Enterprise-program**. Ett slumpmässigt urval av programmen i din Azure AD-klient visas. 

3. Välj **Alla program**på **menyn Programtyp** och välj sedan **Använd**.

4. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

5. Under avsnittet **Hantera** väljer du **Enkel inloggning**. 

6. Välj **Lösenordsbaserad**.

7. Ange URL:en för programmets webbaserade inloggningssida. Den här strängen måste vara den sida som innehåller inmatningsfältet för användarnamn.

   ![Lösenordsbaserad enkel inloggning](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Välj **Spara**. Azure AD försöker tolka inloggningssidan för en användarnamnsinmatning och en lösenordsinmatning. Om försöket lyckas är du klar. 
 
> [!NOTE]
> Nästa steg är att [tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md). När du har tilldelat användare och grupper kan du ange autentiseringsuppgifter som ska användas för en användares räkning när de loggar in på programmet. Välj **Användare och grupper,** markera kryssrutan för användarens eller gruppens rad och klicka sedan på **Uppdatera autentiseringsuppgifter**. Ange sedan användarnamnet och lösenordet som ska användas för användarens eller gruppens räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.
 

## <a name="manual-configuration"></a>Manuell konfiguration

Om Azure AD:s tolkningsförsök misslyckas kan du konfigurera inloggning manuellt.

1. Under ** \<programnamn> konfiguration**väljer du **Konfigurera \<programnamn> Enkel inloggningsinställningar för lösenord** för att visa sidan Konfigurera **inloggning.** 

2. Välj **Identifiera inloggningsfält manuellt**. Ytterligare instruktioner som beskriver manuell identifiering av inloggningsfält visas.

   ![Manuell konfiguration av lösenordsbaserad enkel inloggning](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Välj **Fånga inloggningsfält**. En hämtningsstatussida öppnas på en ny flik och visar att **infångningen av meddelandemetadata pågår**.

4. Om rutan **Tilläggstillägg för Åtkomstpanelen visas** på en ny flik väljer du **Installera nu** för att installera **webbläsartillägget För säker inloggning i mina appar.** (Webbläsartillägget kräver Microsoft Edge, Chrome eller Firefox.) Installera, starta och aktivera tillägget och uppdatera sidan för insamlingsstatus.

   Webbläsartillägget öppnar sedan en annan flik som visar den angivna webbadressen.
5. Gå igenom inloggningsprocessen på fliken med den angivna webbadressen. Fyll i fälten användarnamn och lösenord och försök logga in. (Du behöver inte ange rätt lösenord.)

   En uppmaning ber dig att spara de infångade inloggningsfälten.
6. Välj **OK**. Webbläsartillägget uppdaterar sidan med meddelandet **Metadata har uppdaterats för programmet**. Webbläsarfliken stängs.

7. På inloggningssidan för Azure AD **Configure** väljer **jag Ok, jag kunde logga in på appen**.

8. Välj **OK**.

När inloggningssidan har hämtats kan du tilldela användare och grupper och du kan ställa in principer för autentiseringsuppgifter precis som vanliga [SSO-lösenordsprogram](what-is-single-sign-on.md).

> [!NOTE]
> Du kan ladda upp en panellogotyp för programmet med knappen **Ladda upp logotyp** på fliken **Konfigurera** för programmet.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användarkonton](../app-provisioning/configure-automatic-user-provisioning-portal.md)
