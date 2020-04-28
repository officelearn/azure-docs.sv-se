---
title: Konfigurera enkel inloggning med lösen ord för Azure AD-appar | Microsoft Docs
description: Så här konfigurerar du lösen ord för enkel inloggning (SSO) till dina Azure AD Enterprise-program i Microsoft Identity Platform (Azure AD)
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77063534"
---
# <a name="configure-password-single-sign-on"></a>Konfigurera enkel inloggning för lösen ord

När du [lägger till en Galleri app](add-gallery-app.md) eller en [icke-Galleri-](add-non-gallery-app.md) WEBBAPP till dina Azure AD Enterprise-program, är ett av de alternativ för enkel inloggning som är tillgängligt för dig, [lösenordsbaserad enkel inloggning](what-is-single-sign-on.md#password-based-sso). Det här alternativet är tillgängligt för alla webbplatser med en HTML-inloggnings sida. Lösenordsbaserad SSO, som även kallas lösen ords valv, gör att du kan hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations konton för sociala media. 

Lösenordsbaserad SSO är ett bra sätt att komma igång med att integrera program i Azure AD snabbt och gör att du kan:

-   Aktivera **enkel inloggning för dina användare** genom säker lagring och uppspelning av användar namn och lösen ord för det program som du har integrerat med Azure AD

-   **Stöd för program som kräver flera inloggnings fält** för program som kräver mer än bara användar namn och lösen ords fält för att logga in

-   **Anpassa etiketterna** för fälten användar namn och lösen ord indatatyper som användarna ser på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

-   Tillåt att **användarna** anger sina egna användar namn och lösen ord för alla befintliga program konton som de skriver in manuellt på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

-   Tillåt en **medlem i företags gruppen** att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen för [självbetjänings åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Tillåt en **administratör** att ange ett användar namn och lösen ord som ska användas av personer eller grupper vid inloggning till programmet med hjälp av funktionen uppdatera autentiseringsuppgifter 

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure AD-klient kan du läsa [Lägg till en Galleri app](add-gallery-app.md) eller [lägga till en app som inte är en Galleri](add-non-gallery-app.md).

## <a name="open-the-app-and-select-password-single-sign-on"></a>Öppna appen och välj enkel inloggning för lösen ord

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

2. Gå till **Azure Active Directory** > **företags program**. Ett slumpmässigt exempel på programmen i din Azure AD-klient visas. 

3. I menyn **program typ** väljer du **alla program**och väljer sedan **Använd**.

4. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

5. Under avsnittet **Hantera** väljer du **enkel inloggning**. 

6. Välj **lösenordsbaserad**.

7. Ange webb adressen till programmets webbaserade inloggnings sida. Den här strängen måste vara sidan som innehåller fältet username.

   ![Lösenordsbaserad enkel inloggning](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

8. Välj **Spara**. Azure AD försöker parsa inloggnings sidan för inmatade användar namn och lösen ord. Om försöket lyckas är du klar. 
 
> [!NOTE]
> Nästa steg är att [tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md). När du har tilldelat användare och grupper kan du ange autentiseringsuppgifter som ska användas för en användares räkning när de loggar in i programmet. Välj **användare och grupper**, markera kryss rutan för användarens eller gruppens rad och klicka sedan på **uppdatera autentiseringsuppgifter**. Ange sedan användar namnet och lösen ordet som ska användas för användarens eller gruppens räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna vid start.
 

## <a name="manual-configuration"></a>Manuell konfiguration

Om Azure AD parsing-försöket Miss lyckas kan du konfigurera inloggning manuellt.

1. Under ** \<program namn> konfiguration**väljer du **Konfigurera \<program namn> lösen ords inställningar för enkel inloggning** för att visa sidan **Konfigurera inloggning** . 

2. Välj **identifiera inloggnings fält manuellt**. Ytterligare instruktioner som beskriver manuell identifiering av inloggnings fält visas.

   ![Manuell konfiguration av lösenordsbaserad enkel inloggning](./media/configure-password-single-sign-on/password-configure-sign-on.png)
3. Välj **registrerings fält för registrering**. Sidan status för hämtning öppnas på en ny flik som visar att meddelande om att **metadata har fångats pågår**.

4. Om rutan **åtkomst panels tillägg krävs** på en ny flik väljer du **Installera nu** för att installera webb läsar tillägget **Mina appar säker inloggnings tillägg** . (Webb läsar tillägget kräver Microsoft Edge, Chrome eller Firefox.) Installera, starta och aktivera tillägget och uppdatera sidan för hämtnings status.

   Webb läsar tillägget öppnar sedan en annan flik som visar den angivna URL: en.
5. Gå igenom inloggnings processen på fliken med den angivna URL: en. Fyll i fälten användar namn och lösen ord och försök logga in. (Du behöver inte ange rätt lösen ord.)

   Du uppmanas att spara de insamlade inloggnings fälten.
6. Välj **OK**. Webb läsar tillägget uppdaterar sidan för hämtnings status med meddelandets **metadata har uppdaterats för programmet**. Fliken webbläsare stängs.

7. På sidan **Konfigurera inloggning på** Azure AD väljer **du OK, jag kunde logga in på appen**.

8. Välj **OK**.

När du har skapat inloggnings sidan kan du tilldela användare och grupper, och du kan konfigurera principer för autentiseringsuppgifter precis som vanliga [SSO-program för lösen ord](what-is-single-sign-on.md).

> [!NOTE]
> Du kan ladda upp en panel logo typ för programmet med knappen **Ladda upp logo typ** på fliken **Konfigurera** för programmet.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
