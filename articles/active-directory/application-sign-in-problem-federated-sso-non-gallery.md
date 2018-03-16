---
title: "Problem som loggar in på ett icke-galleriet program konfigurerade för federerad enkel inloggning | Microsoft Docs"
description: "Riktlinjer för de specifika problem som du kan stöta på när du loggar in till ett program som konfigurerats för SAML-baserade federerad enkel inloggning med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: cfd4e478850783983caa3042cbbb2b096a576292
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2018
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problem som loggar in på ett icke-galleriet program som konfigurerats för federerad enkel inloggning

Om du vill felsöka problemet måste du kontrollera i programkonfigurationen i Azure AD som följer:

-   Du har följt alla konfigurationssteg för programmets Azure AD-galleriet.

-   Identifierare och Reply URL som konfigurerats i AAD matchar de förväntade värdena i programmet

-   Du har tilldelat användare till programmet

## <a name="application-not-found-in-directory"></a>Programmet hittades inte i katalog

*Fel AADSTS70001: Program med identifierare ”https://contoso.com' hittades inte i katalogen*.

**Möjlig orsak**

Utfärdaren attribut skickar från programmet till Azure AD i SAML-begäran matchar inte ID-värdet som konfigurerats i Azure AD-programmet.

**Lösning**

Se till att attributet utfärdaren i SAML-begäran matchar identifieraren värdet som konfigurerats i Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  <span id="_Hlk477190042" class="anchor"></span>Gå till **domän och URL: er** avsnitt. Kontrollera att värdet i textrutan identifierare som matchar värdet för ID-värdet som visas i Windows.

När du har uppdaterat ID-värdet i Azure AD och den matchar värdet skickar av programmet i SAML-begäran, bör du kunna logga in till programmet.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svarsadressen matchar inte reply-adresser som har konfigurerats för programmet. 

*Fel AADSTS50011: Svarsadressenhttps://contoso.com' matchar inte reply-adresser som har konfigurerats för programmet* 

**Möjlig orsak** 

AssertionConsumerServiceURL värdet i SAML-begäran matchar inte den Reply URL-värde eller mönster som konfigurerats i Azure AD. AssertionConsumerServiceURL värdet i SAML-begäran är den URL som du ser i felet. 

**Lösning** 

Kontrollera att värdet AssertionConsumerServiceURL i SAML-begäran matchar Reply URL värdet som konfigurerats i Azure AD. 
 
1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.** 

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn. 

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt. 

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory. 

5.  Klicka på **alla program** att visa en lista över alla program. 

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**
  
6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Gå till **domän och URL: er** avsnitt. Kontrollera eller uppdatera värdet i textrutan Reply URL matcha värdet som AssertionConsumerServiceURL i SAML-begäran.

  * Om du inte ser textrutan Reply URL, väljer du den **visa avancerade inställningar för URL: en** kryssrutan. 

När du har uppdaterat Reply URL-värdet i Azure AD och den matchar värdet skickar av programmet i SAML-begäran, bör du kunna logga in till programmet.

## <a name="user-not-assigned-a-role"></a>Användare som inte har tilldelats en roll

*Fel AADSTS50105: Den inloggade användaren 'brian@contoso.com' har inte tilldelats en roll för programmet*

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Lösning**

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

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

Användare som du har valt att kunna starta dessa program med hjälp av de metoder som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran

*Fel AADSTS75005: Begäran är inte ett giltigt Saml2-protokollmeddelande.*

**Möjlig orsak**

Azure AD stöder inte SAML begäran skickas av programmet för enkel inloggning. Några vanliga problem är:

-   Obligatoriska fält i SAML-begäran som saknas

-   SAML-kodade metoden

**Lösning**

1.  Avbilda SAML-begäran. Följ guiden på [felsöka SAML-baserade enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) att lära dig att avbilda SAML-begäran.

2.  Kontakta programvaruleverantören och resursen:

    -   SAML-begäran

    -   [Krav för Azure AD enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

De bör verifiera de stöd för Azure AD SAML-implementeringen för enkel inloggning.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Ingen resurs i requiredResourceAccess lista

*Fel AADSTS65005: Klientprogrammet har begärt åtkomst till resursen ' 00000002-0000-0000-c000-000000000000'. Begäran misslyckades eftersom klienten inte har angetts för den här resursen i listan över requiredResourceAccess*.

**Möjlig orsak**

Programobjektet är skadad.

**Lösning**

Lös problemet genom att ta bort programmet från katalogen. Sedan kan lägga till och konfigurera om programmet, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  Klicka på **ta bort** längst upp till vänster i programmet **översikt** fönstret.

8.  Uppdatera Azure AD och lägga till programmet från Azure AD-galleriet. Konfigurera sedan programmet igen.

När du konfigurerar om programmet bör du kunna logga in på programmet.

## <a name="certificate-or-key-not-configured"></a>Certifikatet eller nyckeln som inte har konfigurerats

Fel AADSTS50003: Inga signeringsnyckeln konfigurerats.

**Möjlig orsak**

Programobjektet är skadat och Azure AD kan identifiera inte certifikatet som konfigurerats för programmet.

**Lösning**

Följ stegen nedan om du vill ta bort och skapa ett nytt certifikat:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmenyn vänstra navigeringsfönstret.

8.  Klicka på **Skapa nytt certifikat** under den **SAML signeringscertifikat** avsnitt.

9.  Välj upphör att gälla. Klicka på **spara.**

10. Kontrollera **aktivera nya certifikatet** att åsidosätta det aktiva certifikatet. Klicka på **spara** överst i fönstret och Godkänn om du vill aktivera det förnyade certifikatet.

11. Under den **SAML-signeringscertifikat** klickar du på **ta bort** att ta bort den **används inte** certifikat.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem när du anpassar SAML-anspråk som skickas till ett program

Information om hur du anpassar SAML attributet anspråk som skickas till ditt program finns [anspråk mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Krav för Azure AD enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
