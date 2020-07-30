---
title: Så här konfigurerar du lösenordsbaserad enkel inloggning för Azure AD-appar
description: Konfigurera lösenordsbaserad enkel inloggning (SSO) för dina Azure AD-program i Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/29/2020
ms.author: kenwith
ms.openlocfilehash: c3f9f96c6429d4925c60a56cd450a9c2ee7dde24
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87419962"
---
# <a name="configure-password-based-single-sign-on"></a>Konfigurera lösenordsbaserad enkel inloggning

I [snabb starts serien](view-applications-portal.md) för program hantering har du lärt dig hur du använder Azure AD som identitets leverantör (IdP) för ett program. I snabb start guiden ställer du in SAML-baserad SSO. Förutom SAML finns det ett alternativ för lösenordsbaserad SSO. Den här artikeln beskriver det lösen ordsbaserade alternativet för enkel inloggning. 

Det här alternativet är tillgängligt för alla webbplatser med en HTML-inloggnings sida. Lösenordsbaserad SSO, som även kallas lösen ords valv, gör att du kan hantera användar åtkomst och lösen ord för webb program som inte stöder identitets Federation. Det är också användbart för scenarier där flera användare behöver dela ett enda konto, t. ex. till din organisations konton för sociala media. 

Lösenordsbaserad SSO är ett bra sätt att komma igång med att integrera program i Azure AD snabbt och gör att du kan:

- Aktivera enkel inloggning för dina användare genom säker lagring och uppspelning av användar namn och lösen ord för det program som du har integrerat med Azure AD

- Stöd för program som kräver flera inloggnings fält för program som kräver mer än bara användar namn och lösen ords fält för att logga in

- Anpassa etiketterna för fälten användar namn och lösen ord indatatyper som användarna ser på [program åtkomst panelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) när de anger sina autentiseringsuppgifter

- Tillåt att användarna anger sina egna användar namn och lösen ord för befintliga program konton som de skriver i manuellt.

- Tillåt en medlem i företags gruppen att ange användar namn och lösen ord som tilldelats en användare med hjälp av funktionen för [självbetjänings åtkomst](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access)

-   Tillåt en administratör att ange ett användar namn och lösen ord som ska användas av personer eller grupper vid inloggning till programmet med hjälp av funktionen uppdatera autentiseringsuppgifter 

## <a name="before-you-begin"></a>Innan du börjar

Att använda Azure AD som identitets leverantör och konfigurera enkel inloggning (SSO) kan vara enkelt eller komplext beroende på vilket program som används. Vissa program kan konfigureras med bara några få åtgärder. Andra kräver djupgående konfiguration. Kom igång snabbt genom att gå igenom [snabb starts serien](view-applications-portal.md) för program hantering. Om det program som du lägger till är enkelt, behöver du förmodligen inte läsa den här artikeln. Om programmet som du lägger till kräver anpassad konfiguration och du behöver använda lösenordsbaserad SSO, är den här artikeln för dig.

> [!IMPORTANT] 
> Det finns vissa scenarier där alternativet för **enkel inloggning** inte kommer att ingå i navigeringen för ett program i **företags program**. 
>
> Om programmet registrerades med **Appregistreringar** konfigureras funktionen för enkel inloggning så att den använder OIDC OAuth som standard. I det här fallet visas inte alternativet för **enkel inloggning** i navigeringen under **företags program**. När du använder **Appregistreringar** för att lägga till din anpassade app konfigurerar du alternativ i manifest filen. Mer information om manifest filen finns i [Azure Active Directory app manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest). Mer information om SSO-standarder finns i [autentisering och auktorisering med Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/authentication-vs-authorization#authentication-and-authorization-using-microsoft-identity-platform). 
>
> Andra scenarier där **enkel inloggning** kommer att saknas i navigeringen är när ett program finns i en annan klient organisation eller om ditt konto inte har de behörigheter som krävs (global administratör, moln program administratör, program administratör eller ägare till tjänstens huvud namn). Behörigheter kan också orsaka ett scenario där du kan öppna **enkel inloggning** men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-configuration"></a>Grundläggande konfiguration

I [snabb starts serien](view-applications-portal.md)har du lärt dig hur du lägger till en app till din klient organisation så att Azure AD vet att den används som identitets leverantör (IdP) för appen. Vissa appar är redan förkonfigurerade och visas i Azure AD-galleriet. Andra appar finns inte i galleriet och du måste skapa en allmän app och konfigurera den manuellt. Beroende på appen kanske det lösen ordsbaserade SSO-alternativet inte är tillgängligt. Om du inte ser den lösenordsbaserade alternativ listan på sidan för enkel inloggning för appen, är den inte tillgänglig.

Konfigurations sidan för lösenordsbaserad enkel inloggning är enkel. Den innehåller bara webb adressen till inloggnings sidan som appen använder. Den här strängen måste vara sidan som innehåller fältet username.

När du har angett URL: en väljer du **Spara**. Azure AD tolkar HTML på inloggnings sidan för fälten användar namn och lösen ord. Om försöket lyckas är du klar.
 
Nästa steg är att [tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md). När du har tilldelat användare och grupper kan du ange autentiseringsuppgifter som ska användas för en användares räkning när de loggar in i programmet. Välj **användare och grupper**, markera kryss rutan för användarens eller gruppens rad och välj sedan **uppdatera autentiseringsuppgifter**. Ange sedan användar namnet och lösen ordet som ska användas för användarens eller gruppens räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna vid start.
 

## <a name="manual-configuration"></a>Manuell konfiguration

Om Azure AD parsing-försöket Miss lyckas kan du konfigurera inloggning manuellt.

1. Under ** \<application name> konfiguration**väljer du **Konfigurera \<application name> Inställningar för enkel inloggning med lösen ord** för att visa sidan **Konfigurera inloggning** . 

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

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
