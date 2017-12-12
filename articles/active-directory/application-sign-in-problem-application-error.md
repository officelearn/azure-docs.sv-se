---
title: "Fel på sidan för ett program när du har loggat in | Microsoft Docs"
description: "Så här löser du problem med Azure AD inloggningen när programmet genererar ett fel"
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
ms.openlocfilehash: bd86d4b13c8f61f278589e5c1d705ad91b3e3d4c
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Fel på sidan för ett program när du loggar in

Azure AD har signerat användaren i i det här scenariot, men programmet visar ett fel som inte tillåter användaren att kunna slutföras inloggning flödet. I det här scenariot programmet inte kan ta emot svar problemet av Azure AD.

Det finns några möjliga orsaker till varför programmet accepterade svaret från Azure AD. Om fel i tillämpningsprogrammet inte är klar att veta vad som saknas i svaret, sedan:

-   Om programmet är Azure AD-galleriet, kontrollerar du att du har följt alla steg i artikeln [felsöka SAML-baserade enkel inloggning till program i Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Använda ett verktyg som [Fiddler](http://www.telerik.com/fiddler) att avbilda SAML-begäran, SAML-svar och SAML-token.

-   Dela SAML-svaret med programvaruleverantören om du vill veta vad som saknas.

## <a name="missing-attributes-in-the-saml-response"></a>Saknade attribut i SAML-svar

Följ stegen nedan om du vill lägga till ett attribut i Azure AD-konfiguration som ska skickas i Azure AD-svaret:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Klicka på **visa och redigera alla andra användare attribut under** den **användarattribut** avsnittet om du vill redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   * Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   * Klicka på **spara.** Du kan se det nya attributet i tabellen.

9.  Spara konfigurationen.

Nästa gång användaren loggar in på programmet, Azure AD skicka det nya attributet i SAML-svaret.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Programmet förväntar sig ett annat användar-ID-värde eller format

Logga in till programmet misslyckas eftersom SAML-svaret saknar attribut, till exempel roller eller eftersom programmet förväntar sig ett annat format för attributet ID för entiteterna.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Lägg till ett attribut i konfigurationen för Azure AD-program:

Följ stegen nedan om du vill ändra värdet för användar-ID:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut**, Välj den unika identifieraren för användarna i den **användar-ID** listrutan.

## <a name="change-entityid-user-identifier-format"></a>Ändra ID för entiteterna (användar-ID)-format

Om programmet förväntar sig ett annat format för attributet ID för entiteterna. Sedan kan du inte väljer ID för entiteterna (användar-ID)-formatet som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD-Välj format för attributet NameID (användar-ID) baserat på värdet valt eller formatet programmet har begärt i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Programmet förväntar sig en annan signaturmetod för SAML-svar

Du vill ändra vilka delar av SAML-token har signerats digitalt av Azure Active Directory. Följ stegen nedan:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Klicka på **visa avancerade inställningar för signering av certifikat** under den **SAML-signeringscertifikat** avsnitt.

9.  Välj lämpliga **signering alternativet** förväntades av programmet:

  * Signera SAML-svar

  * Signera SAML-svar och försäkran

  * Signera SAML-försäkran

Nästa gång användaren loggar in på programmet, Azure AD logga en del av SAML-svar som väljs.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>Programmet förväntar Signeringsalgoritm ska SHA-1

Som standard loggar Azure AD med hjälp av de flesta algoritm SAML-token. Du bör inte ändra logga algoritmen till SHA-1 om det inte krävs av programmet.

Följ stegen nedan om du vill ändra Signeringsalgoritm:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Klicka på **visa avancerade inställningar för signering av certifikat** under den **SAML-signeringscertifikat** avsnitt.

9.  Välj SHA-1, i den **Signeringsalgoritm**.

Nästa gång användaren loggar in på programmet, Azure AD signera SAML-token med hjälp av SHA-1-algoritmen.

## <a name="next-steps"></a>Nästa steg
[Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
