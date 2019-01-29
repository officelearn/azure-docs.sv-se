---
title: Problem som loggar in till ett program från åtkomstpanelen | Microsoft Docs
description: Så här felsöker du problem med åtkomst till ett program från Microsoft Azure AD Access Panel på myapps.microsoft.com
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: japere
ms.openlocfilehash: 529191cf867b6003b0c2eab5ed919087277c7095
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55178043"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problem som loggar in till ett program från åtkomstpanelen

Åtkomstpanelen är en webbaserad portal där en användare med ett arbets- eller skolkonto konto i Azure Active Directory (Azure AD) för att visa och starta molnbaserade program att Azure AD-administratör har gett dem åtkomst till. 

Dessa program är konfigurerade för användarens räkning på Azure AD-portalen. Programmet måste korrekt konfigurerat och tilldelat till användaren eller en grupp som användaren är medlem i vill se programmet på åtkomstpanelen.

Typ av appar som visas kanske en användare finns i följande kategorier:

-   Office 365-program

-   Microsoft och tredje parts program som har konfigurerats med federation-baserad enkel inloggning

-   Lösenordsbaserad SSO-program

-   Program med befintliga lösningar för enkel inloggning

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att din med hjälp av en **webbläsare** som uppfyller minimikraven för åtkomstpanelen.

-   Kontrollera att användarens webbläsare har lagt till URL: en för programmet till dess **betrodda platser**.

-   Se till att kontrollera att programmet är **konfigurerats** korrekt.

-   Kontrollera att användarkontot är **aktiverat** för inloggningar.

-   Kontrollera att användarkontot är **inte låst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömt.**

-   Se till att **Multifaktorautentisering** inte blockerar åtkomst.

-   Se till att en **princip för villkorlig åtkomst** eller **Identity Protection** principen blockerar åtkomst.

-   Se till att en användares **autentiseringskontakt** är uppdaterad så att Multifaktorautentisering eller villkorlig åtkomst-principer ska framtvingas.

-   Se till att även försök rensa cookies i webbläsaren och försök att logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller Webbläsarkrav för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomstpanelen, måste tillägget åtkomstpanelen installeras i användarens webbläsare. Det här tillägget laddas ned automatiskt när en användare väljer ett program som har konfigurerats för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge i Windows 10 Anniversary Edition eller senare

-   Chrome--På Windows 7 eller senare, och i Mac OS X eller senare

-   Firefox 26.0 eller senare, på Windows XP SP2 eller senare, och på Mac OS X 10,6 eller senare

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst till panelen webbläsartillägg

Följ stegen nedan om du vill installera webbläsartillägget för åtkomst till panelen:

1.  Öppna den [åtkomstpanelen](https://myapps.microsoft.com) i en av de webbläsare som stöds och logga in som en **användaren** i din Azure AD.

2.  Klicka på en **lösenord SSO-program** i åtkomstpanelen.

3.  I meddelandet som ber att installera programvaran, väljer **installera nu**.

4.  Baserat på din webbläsare dirigeras du till länken. **Lägg till** tillägget till din webbläsare.

5.  Om webbläsaren ber, väljer du antingen **aktivera** eller **Tillåt** tillägget.

6.  När den har installerats, **starta om** webbläsarsessionen.

7.  Logga in på åtkomstpanelen på och se om kan du **starta** lösenord SSO-program

Du kan också ladda ned tillägget för Chrome och Microsoft Edge från direkt länkarna nedan:

-   [Chrome Access Panel-tillägg](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge Access Panel-tillägg](https://www.microsoft.com/store/apps/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Hur du konfigurerar federerad enkel inloggning för ett Azure AD-galleriprogram

Alla program i Azure AD-galleriet som aktiveras med Enterprise Single Sign-On-funktionen har en stegvis självstudiekurs som är tillgängliga. Du kan komma åt den [lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en stegvis vägledning i detalj.

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application)

-   [Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärdena i programmet (inloggning URL, utfärdare, utloggnings-URL och certifikatet)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Tilldela användare till programmet](#assign-users-to-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  I den **anger du ett namn** textrutan från den **Lägg till från galleriet** Skriv namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textrutan.

9.  Klicka på **Lägg till** för att lägga till programmet.

Du kan se programmets konfigurationsruta efter en kort period.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj **SAML-baserad inloggning** från den **läge** listrutan.

9.  Ange värdena som krävs i **domän och URL: er.** Du bör få värdena från programleverantören.

   1. Om du vill konfigurera programmet som SP-initierad SSO, är inloggnings-URL: en ett obligatoriskt värde. Identifieraren är också ett obligatoriskt värde för vissa program.

   2. Svars-URL är ett obligatoriskt värde för att konfigurera programmet som en IdP-initierad SSO. Identifieraren är också ett obligatoriskt värde för vissa program.

10. **Valfritt:** klickar du på **visa avancerade URL-inställningar** om du vill se de icke krävs.

11. I den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

12. **Valfritt:** klickar du på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

13. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du metadata-URL: er och certifikat som krävs för att konfigurera enkel inloggning med programmet.

14. Klicka på **spara** att spara konfigurationen.

15. Tilldela användare till programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet

Välj användar-ID eller lägga till användarattribut kan du följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet du vill visas här, Använd den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att  **Alla program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för dina användare i den **användaridentifierare** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

    >[!NOTE]
    >Azure AD väljer du formatet för NameID-attributet (användaridentifierare) baserat på värdet valt eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
    >
    >

9.  Lägg till användarattribut, klicka på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

    Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Hur du konfigurerar federerad enkel inloggning för en icke-galleriprogram

Om du vill konfigurera en icke-galleriprogram, måste du ha Azure AD premium och programmet stöder SAML 2.0. Mer information om Azure AD-versioner finns [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   [Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)](#configuring-single-sign-on)

-   [Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärdena i programmet (inloggning URL, utfärdare, utloggnings-URL och certifikatet)](#configuring-single-sign-on)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadatavärden i Azure AD (inloggning URL, identifierare svars-URL)

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte är i Azure AD-galleriet:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram** i den **lägga till din egen app** avsnittet.

7.  Ange namnet på programmet i den **namn** textrutan.

8.  Klicka på **Lägg till** för att lägga till programmet.

9.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

10. Välj **SAML-baserad inloggning** i den **läge** listrutan

11. Ange värdena som krävs i **domän och URL: er.** Du bör få värdena från programleverantören.

  1. Ange svars-URL och identifierare för att konfigurera programmet som en IdP-initierad SSO.

  2. **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO, är inloggnings-URL: en ett obligatoriskt värde.

12. I den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

13. **Valfritt:** klickar du på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2. Klicka på **spara.** Det nya attributet visas i tabellen.

14. Klicka på **konfigurera &lt;programnamn&gt;**  åtkomst dokumentationen om hur du konfigurerar enkel inloggning i programmet. Dessutom har du URL: er med Azure AD och certifikat som krävs för programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användaridentifierare och Lägg till användarattribut ska skickas till programmet

Välj användar-ID eller lägga till användarattribut kan du följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Under den **användarattribut** väljer du den unika identifieraren för dina användare i den **användaridentifierare** listrutan. Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer du formatet för NameID-attributet (användaridentifierare) baserat på värdet valt eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9.  Lägg till användarattribut, klicka på **visa och redigera alla andra användarattribut** kan redigera attributen som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   1. Klicka **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   2 Klicka **spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikat

Följ stegen nedan för att ladda ned programmetadata eller certifikat från Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

    Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för ett Azure AD-galleriprogram

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägga till ett program från Azure AD-galleriet](#add-an-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  I den **anger du ett namn** textrutan från den **Lägg till från galleriet** Skriv namnet på programmet

7.  Välj det program som du vill konfigurera för enkel inloggning

8.  Innan du lägger till programmet, kan du ändra dess namn från den **namn** textrutan.

9.  Klicka på **Lägg till** för att lägga till programmet.

Du kan se programmets konfigurationsruta efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

 * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Tilldela användare till programmet.

10. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du lösenord för enkel inloggning för en icke-galleriprogram

Konfigurera ett program från Azure AD-galleriet som du behöver:

-   [Lägg till en icke-galleriprogram](#add-a-non-gallery-application)

-   [Konfigurera program för lösenord för enkel inloggning](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägg till en icke-galleriprogram

Följ stegen nedan om du vill lägga till ett program från Azure AD-galleriet:

1.  Öppna den [Azure-portalen](https://portal.azure.com) och logga in som en **Global administratör** eller **medadministratör**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på den **Lägg till** knappen i det övre högra hörnet på den **företagsprogram** fönstret.

6.  Klicka på **icke-galleriprogram.**

7.  Ange namnet på ditt program i den **namn** textrutan. Välj **lägga till.**

Du att kunna se programmets konfigurationsruta efter en kort period.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera program för lösenord för enkel inloggning

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

 * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Välj läget **lösenordsbaserad inloggning.**

9.  Ange den **inloggnings-URL**. Det här är URL: en där användare anger sina användarnamn och lösenord för att logga in. Kontrollera fälten inloggning syns i URL: en.

10. Tilldela användare till programmet.

11. Du kan dessutom också ange autentiseringsuppgifter för användarens räkning genom att markera rader av användare och klicka på **uppdaterade autentiseringsuppgifter** och ange användarnamnet och lösenordet åt användarna. Annars kan uppmanas användare att ange autentiseringsuppgifterna sig vid start.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Tilldela en användare till ett program direkt

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** Om du vill **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program i åtkomstpanelen efter en kort period.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här felsökningsstegen gör inte lösa problemet

Öppna ett supportärende med följande information om de är tillgängliga:

-   Korrelations-ID för fel

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsramen under fel inträffar

-   Fiddler-spårningar

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

