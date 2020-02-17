---
title: Problem med att logga in till en federerad Galleri app för enkel inloggning | Microsoft Docs
description: Vägledning för de specifika felen vid inloggning till ett program som du har konfigurerat för SAML-baserad federerad enkel inloggning med Azure AD
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
ms.date: 02/18/2019
ms.author: mimart
ms.reviewer: luleon, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 874d273e26a728afc0a1dc1a16852016797067ca
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367895"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problem med att logga in till ett galleri program som kon figurer ATS för federerad enkel inloggning

För att felsöka inloggnings problemen nedan rekommenderar vi att du följer dessa förslag för att få bättre diagnos och automatisera lösnings stegen:

- Installera [tillägget Mina appar säker webbläsare](access-panel-extension-problem-installing.md) för att hjälpa Azure Active Directory (Azure AD) för att ge bättre diagnos och lösningar när du använder test miljön i Azure Portal.
- Återskapa felet med hjälp av test upplevelsen på sidan konfiguration av app i Azure Portal. Läs mer om [Debug SAML-baserade enkla inloggnings program](../azuread-dev/howto-v1-debug-saml-sso-issues.md)


## <a name="application-not-found-in-directory"></a>Programmet hittades inte i katalogen

*Fel AADSTS70001: det gick inte att hitta programmet med ID: t https:\//contoso.com i katalogen*.

**Möjlig orsak**

Attributet `Issuer` som skickas från programmet till Azure AD i SAML-begäran matchar inte det ID-värde som har kon figurer ATS för programmet i Azure AD.

**Upplösning**

Kontrol lera att attributet `Issuer` i SAML-begäran matchar det ID-värde som kon figurer ATS i Azure AD. Om du använder [test miljön](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure Portal med tillägget mina appars säkra webbläsare, behöver du inte följa dessa steg manuellt.

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör**.

1.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.

1.  Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.

1.  Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

1.  Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

1.  Välj det program som du vill konfigurera för enkel inloggning.

1.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Kontrol lera att värdet i text rutan identifierare matchar värdet för det ID-värde som visas i fel meddelandet.



## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svars adressen stämmer inte överens med de svars adresser som har kon figurer ATS för programmet

*Fel AADSTS50011: svars adressen https:\//contoso.com matchar inte svars adresserna som har kon figurer ATS för programmet*

**Möjlig orsak**

`AssertionConsumerServiceURL` svärdet i SAML-begäran matchar inte svars-URL-värdet eller det mönster som kon figurer ATS i Azure AD. `AssertionConsumerServiceURL` svärdet i SAML-begäran är den URL som visas i fel meddelandet.

**Upplösning**

Se till att `AssertionConsumerServiceURL`-värdet i SAML-begäran matchar svars-URL-värdet som kon figurer ATS i Azure AD. Om du använder [test miljön](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure Portal med tillägget mina appars säkra webbläsare, behöver du inte följa dessa steg manuellt.

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör**.

1.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.

1.  Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.

1.  Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

1.  Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

1.  Välj det program som du vill konfigurera för enkel inloggning.

1.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Verifiera eller uppdatera värdet i text rutan svars-URL så att det matchar `AssertionConsumerServiceURL` svärdet i SAML-begäran.    
    
När du har uppdaterat svars-URL-värdet i Azure AD och det matchar värdet som skickas av programmet i SAML-begäran, bör du kunna logga in på programmet.

## <a name="user-not-assigned-a-role"></a>Användaren har inte tilldelats någon roll

*Fel AADSTS50105: den inloggade användaren brian\@contoso.com har inte tilldelats någon roll för programmet*.

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Upplösning**

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt. Om du använder [test miljön](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure Portal med tillägget mina appars säkra webbläsare, behöver du inte följa dessa steg manuellt.

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör**.

1.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.

1.  Skriv **"Azure Active Directory**" i rutan filtrera sökning och välj **Azure Active Directory** objektet.

1.  Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

1.  Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

1.  I listan med program väljer du det som du vill tilldela en användare till.

1.  När programmet har lästs in väljer **du användare och grupper** från programmets vänstra navigerings meny.

1.  Klicka på knappen **Lägg till** överst i listan **användare och grupper** för att öppna fönstret **Lägg till tilldelning** .

1.  Välj Välj **användare och grupper** i fönstret **Lägg till tilldelning** .

1. I sökrutan **Sök efter namn eller e-postadress** skriver du det fullständiga namnet eller e-postadressen för användaren du vill lägga till.

1. Hovra över **användaren** i listan för att visa en **kryss ruta**. Klicka på kryss rutan bredvid användarens profil bild eller logo typ för att lägga till användaren i den **markerade** listan.

1. **Valfritt:** Om du vill **lägga till fler än en användare**skriver du ett nytt fullständigt namn eller e-postadress till sökrutan **Sök efter namn eller e-postadress** och klickar sedan på kryss rutan för att lägga till användaren i den **markerade** listan.

1. När du är klar med att välja användare klickar du på knappen **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

1. **Valfritt:** Klicka på **Välj roll** väljare i fönstret **Lägg till tilldelning** för att välja en roll att tilldela de användare som du har valt.

1. Klicka på knappen **tilldela** för att tilldela programmet till de valda användarna.

Efter en kort tids period kommer de användare du har valt att kunna starta programmen med de metoder som beskrivs i avsnittet lösnings beskrivning.

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran

*Fel AADSTS75005: begäran är inte ett giltigt Saml2-protokoll meddelande.*

**Möjlig orsak**

Azure AD stöder inte den SAML-begäran som skickas av programmet för enkel inloggning. Några vanliga problem är:

-   Obligatoriska fält som saknas i SAML-begäran
-   SAML-begärandekodad metod

**Upplösning**

1. Avbilda SAML-begäran. Följ självstudien [för att FELSÖKA SAML-baserad enkel inloggning till program i Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md) för att lära dig hur du samlar in SAML-begäran.

1. Kontakta programvaruleverantören och dela följande information:

   -   SAML-begäran

   -   [Krav för SAML-protokoll för enkel inloggning i Azure AD](../develop/single-sign-on-saml-protocol.md)

Program leverantören bör kontrol lera att de stöder Azure AD SAML-implementering för enkel inloggning.

## <a name="misconfigured-application"></a>Felkonfigurerat program

*Fel AADSTS650056: felkonfigurerat program. Detta kan bero på något av följande: klienten har inte listat några behörigheter i de begärda behörigheterna i klientens program registrering. Eller så har administratören inte samtyckt i klient organisationen. Eller kontrol lera program identifieraren i begäran för att se till att den matchar den konfigurerade klient program identifieraren. Kontakta din administratör för att åtgärda konfigurationen eller medgivande för klientens räkning.* .

**Möjlig orsak**

Attributet `Issuer` som skickas från programmet till Azure AD i SAML-begäran matchar inte det ID-värde som har kon figurer ATS för programmet i Azure AD.

**Upplösning**

Kontrol lera att attributet `Issuer` i SAML-begäran matchar det ID-värde som kon figurer ATS i Azure AD. Om du använder [test miljön](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure Portal med tillägget mina appars säkra webbläsare, behöver du inte manuellt följa dessa steg:

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör**.

1.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.

1.  Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.

1.  Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

1.  Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

1.  Välj det program som du vill konfigurera för enkel inloggning.

1.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Kontrol lera att värdet i text rutan identifierare matchar värdet för det ID-värde som visas i fel meddelandet.


## <a name="certificate-or-key-not-configured"></a>Certifikatet eller nyckeln har inte kon figurer ATS

*Fel AADSTS50003: ingen signerings nyckel har kon figurer ATS.*

**Möjlig orsak**

Programobjektet är skadat och Azure AD känner inte igen det certifikat som har kon figurer ATS för programmet.

**Upplösning**

Följ stegen nedan om du vill ta bort och skapa ett nytt certifikat:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör**.

1. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

1. Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.

1. Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

1. Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

1. Välj det program som du vill konfigurera enkel inloggning

1. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

1. Välj **Skapa nytt certifikat** under avsnittet **SAML-signerings certifikat** .

1. Välj förfallo datum och klicka sedan på **Spara**.

1. Kontrol lera att det **nya certifikatet är aktivt** för att åsidosätta det aktiva certifikatet. Klicka sedan på **Spara** överst i fönstret och acceptera för att aktivera förnyelsecertifikatet.

1. Under avsnittet **SAML-signerings certifikat** klickar du på **ta bort** för att ta bort det **oanvända** certifikatet.

## <a name="saml-request-not-present-in-the-request"></a>SAML-begäran saknas i begäran

*Fel AADSTS750054: SAMLRequest eller SAMLResponse måste finnas som frågeparametrar i HTTP-begäran för SAML-omdirigerings bindning.*

**Möjlig orsak**

Azure AD kunde inte identifiera SAML-begäran inom URL-parametrarna i HTTP-begäran. Detta kan inträffa om programmet inte använder HTTP-omdirigering när SAML-begäran skickas till Azure AD.

**Upplösning**

Programmet måste skicka SAML-begäran kodad till plats rubriken med hjälp av HTTP-omdirigering. Mer information om hur du implementerar det finns i avsnittet om HTTP-omdirigeringsbindning i [dokumentet om SAML-protokollets specifikationer](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD skickar token till en felaktig slut punkt

**Möjlig orsak**

Om inloggnings förfrågan inte innehåller en explicit svars-URL (intygs-URL) under enkel inloggning, kommer Azure AD att välja någon av de konfigurerade förlitande URL: erna för programmet. Även om programmet har en explicit svars-URL konfigurerad, kan användaren vara Omdirigerad https://127.0.0.1:444. 

När programmet lades till som en icke-galleriapp skapade Azure Active Directory den här svars-URL:en som ett standardvärde. Det här beteendet har ändrats och Azure Active Directory lägger inte längre till den här URL:en som standard. 

**Upplösning**

Ta bort de oanvända svars-URL: erna som kon figurer ATS för programmet.

1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör**.

2.  Öppna **tillägget Azure Active Directory** genom att välja **alla tjänster** överst i huvud menyn till vänster.

3.  Skriv **"Azure Active Directory"** i rutan filtrera sökning och välj **Azure Active Directory** objektet.

4.  Välj **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Välj **alla program** om du vill visa en lista över alla dina program.

    Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program**.

6.  Välj det program som du vill konfigurera för enkel inloggning.

7.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Ta bort oanvända eller vanliga svars-URL: er som skapats av systemet i **svars-URL: en (intygad konsument tjänst-URL)** . Till exempel `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem vid anpassning av SAML-anspråk som skickas till ett program

Information om hur du anpassar SAML-attributets anspråk som skickas till programmet finns i [anspråks mappning i Azure Active Directory](../develop/active-directory-claims-mapping.md).

## <a name="next-steps"></a>Nästa steg

[Felsöka SAML-baserad enkel inloggning till program i Azure AD](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
