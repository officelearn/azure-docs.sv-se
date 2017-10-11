---
title: "Ett tilldelat program visas inte på åtkomstpanelen | Microsoft Docs"
description: "Felsöka anledningen till ett program inte visas i åtkomstpanelen"
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
ms.reviwer: japere
ms.openlocfilehash: 9ea5744d77b90929598ea5feb80c7bbdff3772fc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="an-assigned-application-is-not-appearing-on-the-access-panel"></a>Ett tilldelat program visas inte på åtkomstpanelen

Åtkomstpanelen är en webbaserad portal som gör att en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har beviljats dem åtkomst till. Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste konfigureras och tilldelats användaren eller en grupp som användaren är medlem i för att se hur programmet åtkomstpanelen.

Typen av appar som visas kanske en användare finns i följande kategorier:

-   Office 365-program

-   Microsoft och tredje parts program som har konfigurerats med federationsbaserat enkel inloggning

-   Lösenordsbaserade SSO-program

-   Program med befintliga lösningar för enkel inloggning

## <a name="general-issues-to-check-first"></a>Allmänna problem med att kontrollera först

-   Om ett program har precis lagts till en användare, försök att logga in och ut igen i användarens åtkomstpanelen efter några minuter att se om programmet har lagts till.

-   Om en licens precis togs bort från en användare eller grupp som användaren är medlem i det här kan ta lång tid, beroende på storleken och komplexiteten i gruppen för ändringar görs. Tillåt extra tid innan du loggar in på åtkomstpanelen.

## <a name="problems-related-to-application-configuration"></a>Problem med konfiguration av program

Ett program kan inte visas i en användares åtkomstpanelen eftersom den inte har konfigurerats korrekt. Nedan visas några exempel på hur du kan felsöka problem som rör programkonfigurationen:

-   [Så här konfigurerar du federerad enkel inloggning för ett program för Azure AD-galleriet](#how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application)

-   [Så här konfigurerar du federerad enkel inloggning för ett program för icke-galleriet](#how-to-configure-federated-single-sign-on-for-a-non-gallery-application)

-   [Så här konfigurerar du ett lösenord för inloggning program för ett program för Azure AD-galleriet](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

-   [Så här konfigurerar du ett lösenord för inloggning program för ett program för icke-galleriet](#how-to-configure-password-single-sign-on-for-a-non-gallery-application)

### <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program för Azure AD-galleriet

Alla program i Azure AD-galleriet aktiverad med Enterprise Single Sign-On-funktionen har en stegvis självstudiekurs som är tillgängliga. Du kan komma åt den [lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) en stegvisa instruktioner för detaljer.

Så här konfigurerar du ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera programmets metadatavärden i Azure AD (logga in på URL: en identifierare, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användar-ID och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadatavärden i programmet (logga in på URL: en, utfärdare, logga ut URL-Adressen och certifikatet)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från galleriet Azure AD:

1.  Öppna den [Azure Portal](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  I den **anger du ett namn** textruta från den **Lägg till från galleriet** avsnittet, skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textruta.

9.  Klicka på **Lägg till** för att lägga till programmet.

Efter en kort period kunna du se programmets konfiguration bladet.

#### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj **SAML-baserade inloggning** från den **läge** listrutan.

9.  Ange obligatoriska värden i **domän och URL: er.** Du bör få värdena från leverantören av tillämpningsprogrammet.

   1. Det är ett obligatoriskt värde för att konfigurera programmet som SP-initierad SSO URL: en inloggning. Identifieraren är också ett obligatoriskt värde för vissa program.

   2. Det är ett obligatoriskt värde för att konfigurera programmet som IdP-initierad SSO Reply-URL. Identifieraren är också ett obligatoriskt värde för vissa program.

10. **Valfritt:** klickar du på **visa avancerade inställningar för URL: en** om du vill se värdena som inte krävs.

11. I den **användarattribut**, Välj den unika identifieraren för användarna i den **användar-ID** listrutan.

12. **Valfritt:** klickar du på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

13. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du metadata URL: er och certifikat som krävs för att konfigurera enkel inloggning med programmet.

14. Klicka på **spara** att spara konfigurationen.

15. Tilldela användare till programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut ska skickas till programmet

Följ stegen nedan om du vill markera användar-ID eller lägga till användarattribut:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet du vill här använder den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för användarna i den **användar-ID** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE] 
   >Azure AD-Välj format för attributet NameID (användar-ID) baserat på värdet valt eller formatet programmet har begärt i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9.  Lägg till användarattribut, klicka på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du ser det nya attributet i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Hämta metadata för Azure AD eller certifikat

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

### <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program för icke-galleriet

Du behöver ha Azure AD premium för att konfigurera ett icke-galleriet program, och programmet stöder SAML 2.0. Mer information om Azure AD-versioner finns [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurera programmets metadatavärden i Azure AD (logga in på URL: en identifierare, Reply URL)](#configuring-single-sign-on)

-   [Välj användar-ID och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadatavärden i programmet (logga in på URL: en, utfärdare, logga ut URL-Adressen och certifikatet)](#configuring-single-sign-on)

#### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadatavärden i Azure AD (logga in på URL: en identifierare, Reply URL)

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte är i Azure AD-galleriet:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  Klicka på **icke-galleriet programmet** i den **lägga till egna app** avsnitt.

7.  Ange namnet på programmet i den **namn** textruta.

8.  Klicka på **Lägg till** för att lägga till programmet.

9.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

10. Välj **SAML-baserade inloggning** i den **läge** listrutan.

11. Ange obligatoriska värden i **domän och URL: er.** Du bör få värdena från leverantören av tillämpningsprogrammet.

   1. Om du vill konfigurera programmet som IdP-initierad SSO, ange Reply-URL och identifierare.

   2.  **Valfritt:** för att konfigurera programmet som SP-initierad SSO URL: en inloggning som den är ett obligatoriskt värde.

12. I den **användarattribut**, Välj den unika identifieraren för användarna i den **användar-ID** listrutan.

13. **Valfritt:** klickar du på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

14. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Du har även Azure AD-URL: er och certifikat som krävs för programmet.

#### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut ska skickas till programmet

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

   >[!NOTE] 
   >Azure AD-Välj format för attributet NameID (användar-ID) baserat på värdet valt eller formatet programmet har begärt i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9.  Lägg till användarattribut, klicka på **visa och redigera andra användarattribut** Redigera attribut som ska skickas till programmet i SAML-token när användaren loggar in.

   Lägg till ett attribut:

   1. Klicka på **Lägg till attributet**. Ange den **namn** och välj den **värdet** i listrutan.

   2. Klicka på **spara.** Du kan se det nya attributet i tabellen.

#### <a name="download-the-azure-ad-metadata-or-certificate"></a>Hämta metadata för Azure AD eller certifikat

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

### <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för Azure AD-galleriet

Så här konfigurerar du ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

#### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från galleriet Azure AD:

1.  Öppna den [Azure Portal](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  I den **anger du ett namn** textruta från den **Lägg till från galleriet** avsnittet, skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textruta.

9.  Klicka på **Lägg till** för att lägga till programmet.

Efter en kort period kunna du se programmets konfiguration bladet.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

10. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

### <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett program för icke-galleriet

Så här konfigurerar du ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett icke-galleriet program](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

#### <a name="add-a-non-gallery-application"></a>Lägga till ett icke-galleriet program

Följ stegen nedan om du vill lägga till ett program från galleriet Azure AD:

1.  Öppna den [Azure Portal](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på de **företagsprogram** bladet.

6.  Klicka på **icke-galleriet program.**

7.  Ange namnet på ditt program i den **namn** textruta. Välj **lägga till.**

Efter en kort period kunna du se programmets konfiguration bladet.

#### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Co-administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

    1.  Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet läses in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läge **lösenordsbaserade inloggning.**

9.  Ange den **inloggnings-URL**. Detta är den URL där användarna anger sina användarnamn och lösenord för att logga in på. Se till att logga in fält som är synliga på URL.

10. [Tilldela användare till programmet](#how-to-assign-a-user-to-an-application-directly).

11. Dessutom kan du också ange autentiseringsuppgifter för användarens räkning genom att markera rader användare och klicka på **referenser uppdatering** och ange användarnamn och lösenord för användarna. Annars uppmanas användarna att ange autentiseringsuppgifterna sig vid start.

## <a name="problems-related-to-assigning-applications-to-users"></a>Problem med att tilldela program till användare

En användare kan inte visas ett program på deras åtkomstpanelen eftersom de inte är tilldelade till programmet. Nedan visas några metoder för att kontrollera:

-   [Kontrollera om en användare är kopplad till programmet](#check-if-a-user-is-assigned-to-the-application)

-   [Tilldela en användare till ett program direkt](#how-to-assign-a-user-to-an-application-directly)

-   [Kontrollera om en användare har tilldelats en licens som hör till programmet](#check-if-a-user-is-under-a-license-related-to-the-application)

-   [Tilldela en licens till en användare](#how-to-assign-a-user-a-license)

### <a name="check-if-a-user-is-assigned-to-the-application"></a>Kontrollera om en användare är kopplad till programmet

Följ stegen nedan om du vill kontrollera om en användare har tilldelats till programmet:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

6.  **Sök** för namnet på programmet i fråga.

7.  Klicka på **användare och grupper**.

8.  Kontrollera om användaren har tilldelats programmet.

   * Annars följer du stegen i ”så här tilldelar du en användare till ett program direkt” att göra detta.

### <a name="how-to-assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör**.

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

Användare som du har valt att kunna starta programmen på åtkomstpanelen efter en kort period.

### <a name="check-if-a-user-is-under-a-license-related-to-the-application"></a>Kontrollera om en användare är under en licens som hör till programmet

Följ stegen nedan om du vill kontrollera en användares tilldelade licenser:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

  * Om användaren har tilldelats en licens denna aktivera första part Office-program ska visas på användarens åtkomstpanelen.

### <a name="how-to-assign-a-user-a-license"></a>Tilldela en användare en licens 

Följ stegen nedan om du vill tilldela en licens till en användare:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser användaren för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfria** klickar du på den **tilldelning alternativ** objektet om du vill tilldela granularly produkter. Klicka på **Ok** när detta har slutförts.

11. Klicka på den **tilldela** för att tilldela licenserna till den här användaren.

## <a name="problems-related-to-assigning-applications-to-groups"></a>Problem med att tilldela program till grupper

En användare visas kanske ett program på deras åtkomstpanelen eftersom de är en del av en grupp som har tilldelats programmet. Nedan visas några metoder för att kontrollera:

-   [Kontrollera en användares gruppmedlemskap](#check-a-users-group-memberships)

-   [Tilldela ett program till en grupp direkt](#how-to-assign-an-application-to-a-group-directly)

-   [Kontrollera om en användare är en del av grupp som har tilldelats en licens](#check-if-a-user-is-part-of-group-assigned-to-a-license)

-   [Tilldela en licens till en grupp](#how-to-assign-a-license-to-a-group)

### <a name="check-a-users-group-memberships"></a>Kontrollera en användares gruppmedlemskap

Följ stegen nedan om du vill kontrollera en grupps medlemskap:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper**.

8.  Kontrollera om användaren är en del av en grupp som tilldelats programmet.

  * Om du vill ta bort användaren från gruppen **klickar du på raden** av grupprinciper och väljer Ta bort.

### <a name="how-to-assign-an-application-to-a-group-directly"></a>Tilldela ett program till en grupp direkt

Följ stegen nedan om du vill tilldela en eller flera grupper till ett program direkt:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör**.

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från Azure Active Directory vänstra navigeringsmenyn.

5.  Klicka på **alla program** att visa en lista över alla program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontrollen längst upp i den **listan med alla program** och ange den **visa** att **alla program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet läses in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** att öppna den **Lägg uppdrag** bladet.

9.  Klicka på den **användare och grupper** selector från den **Lägg uppdrag** bladet.

10. Ange den **fullständig gruppnamn** i gruppen som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **grupp** i listan för att visa en **kryssrutan**. Klickar du på kryssrutan bredvid gruppen profilfoto eller logotyp som du vill lägga till användaren till den **valda** lista.

12. **Valfritt:** om du vill **lägga till fler än en grupp**, typ i en annan **fullständig gruppnamn** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här gruppen till den **valda** lista.

13. När du har valt grupper klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** Väljaren i den **Lägg uppdrag** bladet Välj en roll som ska tilldelas de grupper som du har valt.

15. Klicka på den **tilldela** för att tilldela program till de valda grupperna.

Användare som du har valt att kunna starta programmen på åtkomstpanelen efter en kort period.

### <a name="check-if-a-user-is-part-of-group-assigned-to-a-license"></a>Kontrollera om en användare är en del av grupp som har tilldelats en licens

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla användare**.

6.  **Sök** för den användare som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **grupper**.

8.  Klicka på raden för en viss grupp.

9.  Klicka på **licenser** att se vilka licenser gruppen har tilldelats.

   * Om gruppen har tilldelats en licens för Office detta kan aktivera vissa första part Office-program ska visas på användarens åtkomstpanelen.

### <a name="how-to-assign-a-license-to-a-group"></a>Tilldela en licens till en grupp

Följ stegen nedan om du vill tilldela en licens till en grupp:

1.  Öppna den [ **Azure Portal** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **fler tjänster** längst ned i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **användare och grupper** på navigeringsmenyn.

5.  Klicka på **alla grupper**.

6.  **Sök** för gruppen som du är intresserad av och **klickar du på raden** att välja.

7.  Klicka på **licenser** finns som licenser gruppen för närvarande har tilldelats.

8.  Klicka på den **tilldela** knappen.

9.  Välj **en eller flera produkter** från listan över tillgängliga produkter.

10. **Valfria** klickar du på den **tilldelning alternativ** objektet om du vill tilldela granularly produkter. Klicka på **Ok** när detta har slutförts.

11. Klicka på den **tilldela** för att tilldela licenserna till den här gruppen. Det kan ta lång tid, beroende på storleken och komplexiteten i gruppen.

>[!NOTE]
>Överväg att tillfälligt tilldela en licens till användaren direkt om du vill göra detta snabbare. 
>
>

## <a name="next-steps"></a>Nästa steg
[Lägga till nya användare i Azure Active Directory](active-directory-users-create-azure-portal.md)

