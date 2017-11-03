---
title: "Så här konfigurerar du federerad enkel inloggning för ett icke-galleriet program | Microsoft Docs"
description: "Så här konfigurerar du federerad enkel inloggning för ett anpassat program för icke-galleriet som du vill integrera med Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 781d06d2a8234d4cedf546ef059d49b7ece8783d
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program för icke-galleriet

Konfigurera enkel inloggning för ett icke-galleriet program *utan att skriva kod*, måste du ha en prenumeration eller Azure AD Premium och programmet måste ha stöd för SAML 2.0. Mer information om Azure AD-versioner finns [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Översikt över steg som krävs
Nedan finns en hög nivå översikt över de steg som krävs för att konfigurera federerad enkel inloggning med SAML 2.0 för ett icke-galleriet (t.ex. anpassade) program.

-   [Konfigurera programmets metadatavärden i Azure AD (logga in på URL: en identifierare, Reply URL)](#_Configuring_single_sign-on)

-   [Välj användar-ID och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadatavärden i programmet (logga in på URL: en, utfärdare, logga ut URL-Adressen och certifikatet)](#_Configuring_single_sign-on)

-   [Tilldela användare till programmet](#_Assign_users_to_the_application)

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Konfigurera enkel inloggning till icke-galleriet program

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte är i Azure AD-galleriet:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  Klicka på **icke-galleriet programmet** i den **lägga till egna app** avsnitt

7.  Ange namnet på programmet i den **namn** textruta.

8.  Klicka på **Lägg till** för att lägga till programmet.

9.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

10. Välj **SAML-baserade inloggning** i den **läge** listrutan.

11. Ange obligatoriska värden i **domän och URL: er.** Du bör få värdena från leverantören av tillämpningsprogrammet.

   1. Om du vill konfigurera programmet som IdP-initierad SSO, ange Reply-URL och identifierare.

   2. **Valfritt:** för att konfigurera programmet som SP-initierad SSO URL: en inloggning som den är ett obligatoriskt värde.

12. I den **användarattribut**, Välj den unika identifieraren för användarna i den **användar-ID** listrutan.

13. **Valfritt:** klickar du på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

14. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Du har även Azure AD-URL: er och certifikat som krävs för programmet.

15. [Tilldela användare till programmet.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut ska skickas till programmet

Följ stegen nedan om du vill markera användar-ID eller lägga till användarattribut:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för användarna i den **användar-ID** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

 >[! Observera} Azure AD Välj format för attributet NameID (användar-ID) baserat på det valda värdet eller det format som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
 >
 >

9.  Lägg till användarattribut, klicka på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Hämta metadata för Azure AD eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** avsnittet och klicka sedan på **hämta** värde i kolumnen. Beroende på vilka programmet kräver att konfigurera enkel inloggning, finns antingen alternativet för att hämta Metadata XML eller certifikatet.

Azure AD Ange inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användare** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll att tilldela användare som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda användarna.

Användare som du har valt att kunna starta dessa program med hjälp av de metoder som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Anpassa SAML-anspråk som skickas till ett program

Information om hur du anpassar SAML attributet anspråk som skickas till ditt program finns [anspråk mappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
