---
title: Problem med att logga in på en app med en enda inloggning utan galleri
description: Vägledning för de specifika problem som kan uppstå när du loggar in på ett program som konfigurerats för SAML-baserad federerad enkel inloggning med Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd951f0b4d2f4887630e29cbd3b0ae429b9f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367855"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problem med att logga in på ett program som inte är ett galleri som konfigurerats för federerad enkel inloggning

Om du vill felsöka inloggningsproblemen nedan rekommenderar vi att du följer det här förslaget för att få bättre diagnos och automatisera lösningsstegen:

- Installera tillägget Säker webbläsare för [mina appar](access-panel-extension-problem-installing.md) för att hjälpa Azure Active Directory (Azure AD) att ge bättre diagnos och lösningar när du använder testupplevelsen i Azure-portalen.
- Återskapa felet med hjälp av testupplevelsen på appkonfigurationssidan i Azure-portalen. Läs mer om [Debug SAML-baserade enkel inloggningsprogram](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Programmet hittades inte i katalogen

*Fel AADSTS70001: Det `https://contoso.com` gick inte att hitta programmet med identifierare i katalogen*.

**Möjlig orsak**

Utfärdarattributet skickar från programmet till Azure AD i SAML-begäran matchar inte identifierarens värde som konfigurerats i programmet Azure AD.

**Upplösning**

Kontrollera att `Issuer` attributet i SAML-begäran matchar identifierarvärdet som konfigurerats i Azure AD. Om du använder [testupplevelsen](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med tillägget Säker webbläsare för mina appar behöver du inte följa dessa steg manuellt.

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Kontrollera att värdet i textrutan Identifierare matchar värdet för det identifierarvärde som visas i felet.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svarsadressen matchar inte svarsadresserna som konfigurerats för programmet. 

*Fel AADSTS50011: Svarsadressen `https://contoso.com` matchar inte svarsadresserna som konfigurerats för programmet* 

**Möjlig orsak** 

AssertionConsumerServiceURL-värdet i SAML-begäran matchar inte svars-URL-värdet eller det mönster som konfigurerats i Azure AD. AssertionConsumerServiceURL-värdet i SAML-begäran är den URL som visas i felmeddelandet. 

**Upplösning** 

Kontrollera att `Issuer` attributet i SAML-begäran matchar identifierarvärdet som konfigurerats i Azure AD. Om du använder [testupplevelsen](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med tillägget Säker webbläsare för mina appar behöver du inte följa dessa steg manuellt.
 
1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.** 

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn. 

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.** 

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster. 

5. Klicka på **Alla program om** du vill visa en lista över alla dina program. 

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**
  
6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Verifiera eller uppdatera värdet i textrutan Svara `AssertionConsumerServiceURL` på URL så att värdet i SAML-begäran matchar.    
    
När du har uppdaterat värdet Svara på URL i Azure AD och det matchar värdet som skickas av programmet i SAML-begäran, bör du kunna logga in på programmet.

## <a name="user-not-assigned-a-role"></a>Användaren har inte tilldelats en roll

*Fel AADSTS50105: Den `brian\@contoso.com` inloggade användaren har inte tilldelats en roll för programmet*

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Upplösning**

Om du vill tilldela en eller flera användare till ett program direkt följer du stegen nedan. Om du använder [testupplevelsen](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med tillägget Säker webbläsare för mina appar behöver du inte följa dessa steg manuellt.

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill tilldela en användare till i listan.

7. När programmet har läses in klickar du på **Användare och grupper** från programmets navigeringsmeny till vänster.

8. Klicka på knappen **Lägg** till högst upp i listan **Användare och grupper** för att öppna fönstret Lägg till **tilldelning.**

9. Klicka på väljaren **Användare och grupper** i fönstret Lägg till **tilldelning.**

10. Skriv in det **fullständiga namnet** eller **e-postadressen** till den användare som du är intresserad av att tilldela sökrutan Sök efter **namn eller e-postadress.**

11. Hovra över **användaren** i listan för att visa en **kryssruta**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp om du vill lägga till användaren i listan **Markerad.**

12. **Valfritt:** Om du vill lägga till **fler än en användare**skriver du in ett annat fullständigt namn eller en annan **fullständig** **adress** i sökrutan Sök efter namn **eller e-postadress** och klickar på kryssrutan om du vill lägga till den här användaren i listan **Markerad.**

13. När du är klar med att välja användare klickar du på knappen **Välj** om du vill lägga till dem i listan över användare och grupper som ska tilldelas programmet.

14. **Valfritt:** Klicka på **selectorn Välj roll** i fönstret Lägg till **tilldelning** om du vill välja en roll som du vill tilldela de användare som du har markerat.

15. Klicka på knappen **Tilldela** om du vill tilldela programmet till de markerade användarna.

Efter en kort tid kan de användare som du har valt starta dessa program med de metoder som beskrivs i avsnittet lösningsbeskrivning.

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran

*Fel AADSTS75005: Begäran är inte ett giltigt Saml2-protokollmeddelande.*

**Möjlig orsak**

Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Några vanliga problem är:

-   Obligatoriska fält som saknas i SAML-begäran

-   SAML-begärandekodad metod

**Upplösning**

1.  Fånga SAML-begäran. följ självstudien om [hur du felsöker SAML-baserad enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) för att lära dig hur du samlar in SAML-begäran.

2.  Kontakta programleverantören och dela:

    -   SAML-begäran

    -   [Azure AD-krav för enstaka inloggnings-SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Programleverantören bör verifiera att de stöder Azure AD SAML-implementeringen för enkel inloggning.

## <a name="misconfigured-application"></a>Felkonfigurerat program

*Fel AADSTS650056: Felaktigt konfigurerat program. Detta kan bero på något av följande: Klienten har inte angett några behörigheter i de begärda behörigheterna i klientens programregistrering. Eller, Administratören har inte samtyckt till i klienten. Kontrollera också programidentifieraren i begäran för att säkerställa att den matchar den konfigurerade klientprogramidentifieraren. Kontakta administratören för att åtgärda konfigurationen eller samtycket för klientens räkning.*

**Möjlig orsak**

Attributet `Issuer` som skickas från programmet till Azure AD i SAML-begäran matchar inte identifierarvärdet som konfigurerats för programmet i Azure AD.

**Upplösning**

Kontrollera att `Issuer` attributet i SAML-begäran matchar identifierarvärdet som konfigurerats i Azure AD. Om du använder [testupplevelsen](../azuread-dev/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med tillägget Säker webbläsare för Mina appar behöver du inte göra så här manuellt:

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör**.

1.  Öppna **Azure Active Directory-tillägget** genom att välja **Alla tjänster** högst upp på huvudnavigeringsmenyn.

1.  Skriv **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

1.  Välj **Företagsprogram** på menyn Azure Active Directory till vänster.

1.  Välj **Alla program om** du vill visa en lista över alla dina program.

    Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och ställer in alternativet **Visa** till **Alla program**.

1.  Välj det program som du vill konfigurera för enkel inloggning.

1.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. Kontrollera att värdet i textrutan Identifierare matchar värdet för det identifierarvärde som visas i felet.

## <a name="certificate-or-key-not-configured"></a>Certifikat eller nyckel är inte konfigurerad

Fel AADSTS50003: Ingen signeringsnyckel har konfigurerats.

**Möjlig orsak**

Programobjektet är skadat och Azure AD känner inte igen certifikatet som konfigurerats för programmet.

**Upplösning**

Så här tar du bort och skapar ett nytt certifikat:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Klicka på **Skapa nytt certifikat** under avsnittet **SAML-signeringscertifikat.**

9. Välj Utgångsdatum. Klicka sedan på **Spara.**

10. Markera **Gör ett nytt certifikat aktivt** för att åsidosätta det aktiva certifikatet. Klicka sedan på **Spara** överst i fönstret och acceptera för att aktivera förnyelsecertifikatet.

11. Klicka på **Ta bort** det **oanvända** certifikatet under avsnittet **SAML-signeringscertifikat.**

## <a name="saml-request-not-present-in-the-request"></a>SAML-begäran finns inte i begäran

*Fel AADSTS750054: SAMLRequest eller SAMLResponse måste finnas som frågesträngparametrar i HTTP-begäran om SAML-omdirigeringsbindning.*

**Möjlig orsak**

Azure AD kunde inte identifiera SAML-begäran inom URL-parametrarna i HTTP-begäran. Detta kan inträffa om programmet inte använder HTTP-omdirigeringsbindning när SAML-begäran skickas till Azure AD.

**Upplösning**

Programmet måste skicka SAML-begäran som kodats till platshuvudet med hjälp av HTTP-omdirigeringsbindning. Mer information om hur du implementerar det finns i avsnittet om HTTP-omdirigeringsbindning i [dokumentet om SAML-protokollets specifikationer](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD skickar token till en felaktig slutpunkt

**Möjlig orsak**

Om inloggningsbegäran inte innehåller en explicit svars-URL (Url för kontroll konsumenttjänst) väljer Azure AD någon av de konfigurerade förlitar-URL:erna för det programmet under enkel inloggningsbegäran. Även om programmet har en explicit svars-URL konfigurerad https://127.0.0.1:444kan användaren omdirigeras . 

När programmet lades till som en icke-galleriapp skapade Azure Active Directory den här svars-URL:en som ett standardvärde. Det här beteendet har ändrats och Azure Active Directory lägger inte längre till den här URL:en som standard. 

**Upplösning**

Ta bort de oanvända svarsadresserna som konfigurerats för programmet.

1.  Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör**.

2.  Öppna **Azure Active Directory-tillägget** genom att välja **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Välj **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Välj **Alla program om** du vill visa en lista över alla dina program.

    Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och ställer in alternativet **Visa** till **Alla program**.

6.  Välj det program som du vill konfigurera för enkel inloggning.

7.  När programmet läses in öppnar du **Grundläggande SAML-konfiguration**. I **svars-URL:en (Url för kontroll konsumenttjänst)** tar du bort oanvända eller standardsvarsadresser för svar som skapats av systemet. Till exempel `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem när saml-anspråk som skickas till ett program anpassas

Mer information om hur du anpassar SAML-attributanspråk som skickas till ditt program finns [i Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Azure AD-krav för enstaka inloggnings-SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
