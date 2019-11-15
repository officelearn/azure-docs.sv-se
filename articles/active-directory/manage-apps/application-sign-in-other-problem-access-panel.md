---
title: Problem med att logga in till ett program från åtkomst panelen | Microsoft Docs
description: Så här felsöker du problem med att komma åt ett program från Microsoft Azure AD åtkomst panelen på myapps.microsoft.com
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
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b93ee38666b93253c7cda6c756d4f58daaea236
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082147"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problem med att logga in till ett program från åtkomst panelen

Åtkomst panelen är en webbaserad portal som gör det möjligt för en användare med ett arbets-eller skol konto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har beviljat åtkomst till. 

Dessa program är konfigurerade för användarens räkning i Azure AD-portalen. Programmet måste vara korrekt konfigurerat och tilldelat användaren eller en grupp som användaren är medlem i för att kunna se programmet i åtkomst panelen.

Den typ av appar som en användare kan se hamnar i följande kategorier:

-   Office 365-program

-   Program från Microsoft och tredje part som kon figurer ATS med Federation-baserad SSO

-   Lösenordsbaserade SSO-program

-   Program med befintliga SSO-lösningar

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrol lera först

-   Kontrol lera att du använder en **webbläsare** som uppfyller minimi kraven för åtkomst panelen.

-   Kontrol lera att användarens webbläsare har lagt till URL: en för programmet på **Betrodda platser**.

-   Kontrol lera att programmet har **kon figurer ATS** korrekt.

-   Kontrol lera att användar kontot är **aktiverat** för inloggningar.

-   Kontrol lera att användarens konto **inte är utelåst.**

-   Kontrol lera att användarens **lösen ord inte har upphört att gälla eller är bortglömt.**

-   Kontrol lera att **Multi-Factor Authentication** inte blockerar användar åtkomsten.

-   Se till att en **princip för villkorlig åtkomst** eller **identitets skydds** princip inte blockerar användar åtkomst.

-   Se till att en användares **kontakt information för autentisering** är uppdaterad för att tillåta att Multi-Factor Authentication eller villkorliga åtkomst principer tillämpas.

-   Se till att du också försöker rensa webbläsarens cookies och försöka logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Mötes webbläsarens krav på åtkomst panelen

Åtkomst panelen kräver en webbläsare som stöder Java Script och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) i åtkomst panelen måste åtkomst panels tillägget vara installerat i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som har kon figurer ATS för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan användarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge på Windows 10-jubileums version eller senare

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26,0 eller senare –-på Windows XP SP2 eller senare och på Mac OS X 10,6 eller senare

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du åtkomst panelens webb läsar tillägg

Om du vill installera åtkomst panelens webb läsar tillägg följer du stegen nedan:

1.  Öppna [åtkomst panelen](https://myapps.microsoft.com) i någon av de webbläsare som stöds och logga in som en **användare** i Azure AD.

2.  Klicka på ett **Password-SSO-program** på åtkomst panelen.

3.  I uppmana att du uppmanas att installera program varan väljer du **Installera nu**.

4.  Baserat på din webbläsare dirigeras du till nedladdnings länken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du antingen **Aktivera** eller **Tillåt** tillägget.

6.  **Starta om** webbläsarsessionen när den har installerats.

7.  Logga in på åtkomst panelen och se om du kan **Starta** dina inloggnings program för lösen ord

Du kan också ladda ned tillägget för Chrome och Microsoft Edge från de direkta länkarna nedan:

-   [Tillägg för Chrome Access panel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Microsoft Edge Access panel-tillägg](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett Azure AD Gallery-program

Alla program i Azure AD-galleriet som är aktiverade med funktionen för enkel inloggning i Enterprise har en stegvis själv studie kurs tillgänglig. Du kan få åtkomst till [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-guide.

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   Lägga till ett program från Azure AD-galleriet

-   [Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Tilldela användare till programmet

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet.

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på knappen **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj **SAML-baserad inloggning** från List rutan **läge** .

9. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

   1. För att konfigurera programmet som SP-initierad SSO, är inloggnings-URL: en obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

   2. Om du vill konfigurera programmet som IdP-initierat SSO, är svars-URL ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

10. **Valfritt:** Klicka på **Visa avancerade URL-inställningar** om du vill se de värden som inte krävs.

11. I användarattribut **väljer**du den unika identifieraren för dina användare i list rutan **användar identifierare** .

12. **Valfritt:** Klicka på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

13. Klicka på **konfigurera &lt;program namn&gt;** för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också de metadata-URL: er och certifikat som krävs för att konfigurera SSO med programmet.

14. Klicka på **Spara** för att spara konfigurationen.

15. Tilldela användare till programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ stegen nedan om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filter** kontrollen längst upp i **listan Alla program** och anger alternativet **Visa** för **alla program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Under avsnittet **användarattribut** väljer du den unika identifieraren för dina användare i list rutan **användar identifierare** . Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i artikeln [Single Sign-on SAML Protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) i avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

   2. Klicka på **Spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ stegen nedan om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumn värde. Beroende på vad programmet kräver för att konfigurera enkel inloggning, ser du antingen alternativet att ladda ned XML-metadata eller certifikatet.

   Azure AD tillhandahåller inte en URL för att hämta metadata. Det går bara att hämta metadata som en XML-fil.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du federerad enkel inloggning för ett program som inte är ett galleri program

Om du vill konfigurera ett program som inte är ett galleri program måste du ha Azure AD Premium och programmet har stöd för SAML 2,0. Mer information om Azure AD-versioner finns i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)

-   [Välj användar-ID och Lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och certifikat](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurera Azure AD metadata-värden i programmet (inloggnings-URL, utfärdare, utloggnings-URL och certifikat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadata-värden i Azure AD (inloggnings-URL, identifierare, svars-URL)

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program som inte finns i Azure AD-galleriet:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6. Klicka på **program som inte är Galleri** i avsnittet **Lägg till din egen app** .

7. Ange namnet på programmet i text rutan **namn** .

8. Klicka på knappen **Lägg till** för att lägga till programmet.

9. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

10. Välj **SAML-baserad inloggning** i list rutan **läge**

11. Ange de värden som krävs i **domän och URL: er.** Du bör hämta dessa värden från program leverantören.

    1. Ange svars-URL och identifierare för att konfigurera programmet som IdP-initierad SSO.

    2. **Valfritt:** För att konfigurera programmet som SP-initierad SSO, är inloggnings-URL: en obligatoriskt värde.

12. I användarattribut **väljer**du den unika identifieraren för dina användare i list rutan **användar identifierare** .

13. **Valfritt:** Klicka på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka på **konfigurera &lt;program namn&gt;** för att få åtkomst till dokumentation om hur du konfigurerar enkel inloggning i programmet. Dessutom har du Azure AD-URL: er och certifikat som krävs för programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj användar-ID och Lägg till användarattribut som ska skickas till programmet

Följ stegen nedan om du vill välja användar-ID eller lägga till användarattribut:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Under avsnittet **användarattribut** väljer du den unika identifieraren för dina användare i list rutan **användar identifierare** . Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer formatet för attributet NameID (användar identifierare) baserat på det värde som valts eller det format som begärdes av programmet i SAML-AuthRequest. Mer information finns i artikeln [Single Sign-on SAML Protocol](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) i avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigera alla andra** användarattribut för att redigera de attribut som ska skickas till programmet i SAML-token när användarna loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värdet** i list rutan.

   2 Klicka på **Spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller certifikatet

Följ stegen nedan om du vill ladda ned metadata för programmet eller certifikatet från Azure AD:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumn värde. Beroende på vad programmet kräver för att konfigurera enkel inloggning, ser du antingen alternativet att ladda ned XML-metadata eller certifikatet.

   Azure AD tillhandahåller inte en URL för att hämta metadata. Det går bara att hämta metadata som en XML-fil.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   Lägga till ett program från Azure AD-galleriet

-   Konfigurera programmet för enkel inloggning med lösen ord

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  I text rutan **Ange ett namn** i avsnittet **Lägg till från galleriet** skriver du namnet på programmet

7.  Välj programmet du vill konfigurera för enkel inloggning

8.  Innan du lägger till programmet kan du ändra dess namn i text rutan **namn** .

9.  Klicka på knappen **Lägg till** för att lägga till programmet.

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. Tilldela användare till programmet.

10. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Så här konfigurerar du enkel inloggning för lösen ord för ett program som inte är ett galleri program

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägg till ett program som inte är ett galleri program](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning med lösen ord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägg till ett program som inte är ett galleri program

Följ stegen nedan om du vill lägga till ett program i Azure AD-galleriet:

1.  Öppna [Azure Portal](https://portal.azure.com) och logga in som **Global administratör** eller **medadministratör**

2.  Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **företags program** .

6.  Klicka på **program som inte är Galleri.**

7.  Ange namnet på ditt program i text rutan **namn** . Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurations fönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning med lösen ord

Följ stegen nedan om du vill konfigurera enkel inloggning för ett program:

1. Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **Global administratör** eller **medadministratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning för.

7. När programmet har lästs in klickar du på **enkel inloggning** från programmets vänstra navigerings meny.

8. Välj läge för **lösenordsbaserad inloggning.**

9. Ange **inloggnings-URL: en**. Detta är URL: en där användarna anger sitt användar namn och lösen ord för att logga in. Se till att inloggnings fälten visas på URL: en.

10. Tilldela användare till programmet.

11. Dessutom kan du ange autentiseringsuppgifter för användarens räkning genom att markera användarens rader och klicka på **uppdatera autentiseringsuppgifter** och ange användar namn och lösen ord för användarnas räkning. Annars uppmanas användarna att ange sina autentiseringsuppgifter vid start.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2. Öppna **tillägget Azure Active Directory** genom att klicka på **alla tjänster** överst i den vänstra navigerings menyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företags program** från Azure Active Directory vänstra navigerings menyn.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill tilldela en användare i listan.

7. När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigerings meny.

8. Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9. Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

Efter en kort period kan de användare du har valt kunna starta dessa program på åtkomst panelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om de här fel söknings stegen inte löser problemet

öppna ett support ärende med följande information om den är tillgänglig:

-   Korrelations fel-ID

-   UPN (användarens e-postadress)

-   TenantID

-   Typ av webbläsare

-   Tidszon och tid/tidsram vid fel inträffar

-   Fiddler-spår

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

