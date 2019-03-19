---
title: Problem som loggar in på en icke-galleriprogram som konfigurerats för federerad enkel inloggning | Microsoft Docs
description: Vägledning för specifika problem som du kan stöta på när du loggar in till ett program som konfigurerats för SAML-baserad federerad enkel inloggning med Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cb0139227012be610977df3240f74a03f53be7e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009069"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problem som loggar in på en icke-galleriprogram som konfigurerats för federerad enkel inloggning

Om du vill felsöka de problem med användarinloggning nedan, rekommenderar vi att du följer dessa förslag för att få bättre diagnostik och automatisera Lösningssteg:

- Installera den [Mina appar säkra webbläsartillägget](access-panel-extension-problem-installing.md) för att Azure Active Directory (AD Azure) att ge bättre diagnoser och lösningar när du använder testning upplevelse i Azure-portalen.
- Återskapa felet med hjälp av belastningstester experience i app-konfigurationssidan i Azure-portalen. Läs mer på [felsöka SAML-baserad enkel inloggning för program](../develop/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Program som inte finns i katalogen

*Fel AADSTS70001: Programmet med ID: T `https://contoso.com` hittades inte i katalogen*.

**Möjlig orsak**

Utfärdaren attributet skickar från programmet till Azure AD i SAML-begäran matchar inte det identifierarvärde som konfigurerats i Azure AD-programmet.

**Lösning**

Se till att den `Issuer` attribut i SAML-begäran matchar identifierarvärde som konfigurerats i Azure AD. Om du använder den [testning upplevelse](../develop/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med Mina appar säker webbläsartillägget behöver du inte manuellt följer du dessa steg.

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. När programmet läses in, öppnar du **grundläggande SAML-konfiguration**. Kontrollera att värdet i textrutan identifierare som matchar värdet för ID-värde som visas i felet.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svarsadressen matchar inte svars-adresser som har konfigurerats för programmet. 

*Fel AADSTS50011: Svarsadressen `https://contoso.com` matchar inte svars-adresser som har konfigurerats för programmet* 

**Möjlig orsak** 

AssertionConsumerServiceURL värdet i SAML-begäran matchar inte den svars-URL-värde eller mönster som konfigurerats i Azure AD. AssertionConsumerServiceURL värdet i SAML-begäran är den URL som visas i felet. 

**Lösning** 

Se till att den `Issuer` attribut i SAML-begäran matchar identifierarvärde som konfigurerats i Azure AD. Om du använder den [testning upplevelse](../develop/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med Mina appar säker webbläsartillägget behöver du inte manuellt följer du dessa steg.
 
1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.** 

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn. 

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt. 

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory. 

5. Klicka på **alla program** att visa en lista över alla dina program. 

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**
  
6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. När programmet läses in, öppnar du **grundläggande SAML-konfiguration**. Kontrollera eller uppdatera värdet i textrutan svars-URL så att den matchar den `AssertionConsumerServiceURL` värdet i SAML-begäran.    
    
När du har uppdaterat svars-URL-värdet i Azure AD och den matchar det värde som skickas av programmet i SAML-begäran, bör du kunna logga in till programmet.

## <a name="user-not-assigned-a-role"></a>Användare som inte har tilldelats en roll

*Fel AADSTS50105: Den inloggade användaren `brian\@contoso.com` inte har tilldelats en roll för programmet*

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Lösning**

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt. Om du använder den [testning upplevelse](../develop/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med Mina appar säker webbläsartillägget behöver du inte manuellt följer du dessa steg.

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran

*Fel AADSTS75005: Begäran är inte ett giltigt Saml2-protokollmeddelande.*

**Möjlig orsak**

Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Några vanliga problem är:

-   Obligatoriska fält saknas i SAML-begäran

-   SAML-begäranskodad metod

**Lösning**

1.  Avbilda SAML-begäran. kursen [Felsök SAML-baserad enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) och lär dig att samla in SAML-begäran.

2.  Kontakta programvaruleverantören och dela:

    -   SAML-begäran

    -   [Azure AD enkel inloggning SAML-protokollkrav](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Programvaruleverantören bör verifiera att de stöder Azure AD SAML-implementering för enkel inloggning.

## <a name="misconfigured-application"></a>Felkonfigurerad program

*Fel AADSTS650056: Felkonfigurerad program. Detta kan bero på något av följande: Klienten har inte finns några behörigheter för 'AAD Graph' begärda behörigheter i klientens programregistrering. Eller administratören har inte godkänt i klienten. Eller Kontrollera program-ID i begäran för att se till att den matchar den konfigurerade klient-ID för programmet. Kontakta din administratör om du vill åtgärda konfigurationen eller ge samtycke åt klienten.* .

**Möjlig orsak**

Den `Issuer` attribut som skickas från programmet till Azure AD i SAML-begäran matchar inte det identifierarvärde som konfigurerats för programmet i Azure AD.

**Lösning**

Se till att den `Issuer` attribut i SAML-begäran matchar identifierarvärde som konfigurerats i Azure AD. Om du använder den [testning upplevelse](../develop/howto-v1-debug-saml-sso-issues.md) i Azure-portalen med Mina appar säker webbläsartillägget behöver du inte manuellt följer du dessa steg:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **medadministratör**.

1.  Öppna den **Azure Active Directory-tillägget** genom att välja **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

1.  Typ **”Azure Active Directory”** i filtret sökrutan och välj den **Azure Active Directory** objekt.

1.  Välj **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

1.  Välj **alla program** att visa en lista över alla dina program.

    Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program**.

1.  Välj det program som du vill konfigurera för enkel inloggning.

1.  När programmet läses in, öppnar du **grundläggande SAML-konfiguration**. Kontrollera att värdet i textrutan identifierare som matchar värdet för ID-värde som visas i felet.

## <a name="certificate-or-key-not-configured"></a>Certifikat eller nyckel har inte angetts

Fel AADSTS50003: Inga signeringsnyckel som konfigurerats.

**Möjlig orsak**

Programobjektet är skadad och Azure AD kan identifiera inte certifikatet som konfigurerats för programmet.

**Lösning**

Ta bort och skapa ett nytt certifikat genom att följa stegen nedan:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Klicka på **Skapa nytt certifikat** under den **SAML-signeringscertifikat** avsnittet.

9. Välj utgångsdatum. Klicka sedan på **spara.**

10. Kontrollera **gör nytt certifikat aktivt** att åsidosätta det aktiva certifikatet. Klicka sedan på **spara** överst i fönstret och acceptera för att aktivera förnyelsecertifikatet.

11. Under den **SAML-signeringscertifikat** klickar du på **ta bort** att ta bort den **används inte** certifikat.

## <a name="saml-request-not-present-in-the-request"></a>SAML-begäran finns inte i begäran

*Fel AADSTS750054: SAMLRequest eller SAMLResponse måste vara närvarande enligt fråga strängparametrar i HTTP-begäran för SAML omdirigera bindning.*

**Möjlig orsak**

Azure AD inte kan identifiera SAML-begäran i URL-parametrar i HTTP-begäran. Detta kan inträffa om programmet inte använder HTTP-omdirigering bindning när du skickar SAML-begäran till Azure AD.

**Lösning**

Programmet måste skicka SAML-begäranskodad i location-huvudet med HTTP omdirigera bindning. Mer information om hur du implementerar den, finns i avsnittet HTTP omdirigera bindning i den [SAML-protokollet specifikationsdokument](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD skickar token till en felaktig slutpunkt

**Möjlig orsak**

Om inloggning-begäran inte innehåller en explicit svars-URL (försäkran URL för Konsumenttjänst) och sedan Azure AD väljer någon av de konfigurerade under enkel inloggning, använder du URL: er för programmet. Även om programmet har en explicit svars-URL har konfigurerats kan användaren kan vara att omdirigeras https://127.0.0.1:444. 

När programmet lades till som en icke-galleriapp skapade Azure Active Directory den här svars-URL:en som ett standardvärde. Det här beteendet har ändrats och Azure Active Directory lägger inte längre till den här URL:en som standard. 

**Lösning**

Ta bort oanvända svars-URL som konfigurerats för programmet.

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att välja **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Typ **”Azure Active Directory”** i filtret sökrutan och välj den **Azure Active Directory** objekt.

4.  Välj **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Välj **alla program** att visa en lista över alla dina program.

    Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program**.

6.  Välj det program som du vill konfigurera för enkel inloggning.

7.  När programmet läses in, öppnar du **grundläggande SAML-konfiguration**. I den **svars-URL (försäkran URL för Konsumenttjänst)**, ta bort oanvända eller standard svars-URL: er som skapats av systemet. Till exempel `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem när du anpassar SAML-anspråk som skickas till ett program

Läs hur du anpassar SAML attributet anspråk som skickas till programmet i [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Azure AD enkel inloggning SAML-protokollkrav](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
