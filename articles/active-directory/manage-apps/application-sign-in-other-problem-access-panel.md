---
title: Problem med att logga in på ett program från åtkomstpanelen | Microsoft-dokument
description: Felsöka problem med åtkomst till ett program från Microsoft Azure AD Access Panel på myapps.microsoft.com
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082147"
---
# <a name="problems-signing-in-to-an-application-from-the-access-panel"></a>Problem med att logga in på ett program från åtkomstpanelen

Åtkomstpanelen är en webbaserad portal som gör det möjligt för en användare med ett arbets- eller skolkonto i Azure Active Directory (Azure AD) att visa och starta molnbaserade program som Azure AD-administratören har gett dem åtkomst till. 

Dessa program konfigureras för användarens räkning i Azure AD-portalen. Programmet måste konfigureras korrekt och tilldelas användaren eller en grupp som användaren är medlem i för att kunna se programmet på åtkomstpanelen.

Den typ av appar som en användare ser faller i följande kategorier:

-   Office 365-program

-   Microsoft- och tredjepartsprogram som konfigurerats med federationsbaserad SSO

-   Lösenordsbaserade SSO-program

-   Program med befintliga SSO-lösningar

## <a name="general-issues-to-check-first"></a>Allmänna problem att kontrollera först

-   Kontrollera att du använder en **webbläsare** som uppfyller minimikraven för åtkomstpanelen.

-   Kontrollera att användarens webbläsare har lagt till url:en för programmet på dess **betrodda platser**.

-   Kontrollera att programmet är **korrekt konfigurerat.**

-   Kontrollera att användarens konto är **aktiverat** för inloggningar.

-   Kontrollera att användarens konto inte är **utelåst.**

-   Kontrollera att användarens **lösenord inte har upphört att gälla eller glömts bort.**

-   Kontrollera att **multifaktorautentisering** inte blockerar användaråtkomst.

-   Kontrollera att en **princip för villkorlig åtkomst** eller **identitetsskydd** inte blockerar användaråtkomst.

-   Kontrollera att en användares **autentiseringskontaktinformation** är uppdaterad så att principer för multifaktorautentisering eller villkorlig åtkomst kan tillämpas.

-   Se till att också försöka rensa webbläsarens cookies och försöka logga in igen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Uppfyller webbläsarkraven för åtkomstpanelen

Åtkomstpanelen kräver en webbläsare som stöder JavaScript och har CSS aktiverat. Om du vill använda lösenordsbaserad enkel inloggning (SSO) på åtkomstpanelen måste tillägget för åtkomstpanelen installeras i användarens webbläsare. Det här tillägget hämtas automatiskt när en användare väljer ett program som är konfigurerat för lösenordsbaserad SSO.

För lösenordsbaserad SSO kan slutanvändarens webbläsare vara:

-   Internet Explorer 8, 9, 10, 11 – på Windows 7 eller senare

-   Microsoft Edge på Windows 10 Anniversary Edition eller senare

-   Chrome – på Windows 7 eller senare och på MacOS X eller senare

-   Firefox 26.0 eller senare – på Windows XP SP2 eller senare, och på Mac OS X 10.6 eller senare

## <a name="how-to-install-the-access-panel-browser-extension"></a>Så här installerar du webbläsartillägget för åtkomstpanelen

Så här installerar du webbläsartillägget för åtkomstpanelen:

1.  Öppna [åtkomstpanelen](https://myapps.microsoft.com) i en av webbläsarna som stöds och logga in som **användare** i din Azure AD.

2.  Klicka på ett **lösenords-SSO-program** på åtkomstpanelen.

3.  I prompten som ber om att installera programvaran väljer du **Installera nu**.

4.  Baserat på din webbläsare är du riktad till nedladdningslänken. **Lägg till** tillägget i webbläsaren.

5.  Om webbläsaren frågar väljer du att **aktivera** eller **tillåt** tillägget.

6.  Starta om webbläsarsessionen **när** den har installerats.

7.  Logga in på åtkomstpanelen och se om du kan **starta** dina lösenords-SSO-program

Du kan också hämta tillägget för Chrome och Microsoft Edge från de direkta länkarna nedan:

-   [Tillägg till panelen Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Tillägg till Microsoft Edge-åtkomstpanelen](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84)

## <a name="how-to-configure-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera federerad enkel inloggning för ett Azure AD-galleriprogram

Alla program i Azure AD-galleriet som är aktiverat med Enterprise Single Sign-On-funktionen har en steg-för-steg-självstudie. Du kan komma åt [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en detaljerad steg-för-steg-vägledning.

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   Lägga till ett program från Azure AD-galleriet

-   [Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   [Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och -certifikat](#download-the-azure-ad-metadata-or-certificate)

-   [Konfigurera Azure AD-metadatavärden i programmet (Logga in på URL, Utfärdare, URL för utloggning och certifikat)](#configure-single-sign-on-for-an-application-from-the-azure-ad-gallery)

-   Tilldela användare till programmet

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Skriv namnet på programmet i textrutan **Ange ett namn** från **galleriet.**

7.  Välj det program som du vill konfigurera för enkel inloggning.

8.  Innan du lägger till programmet kan du ändra dess namn från textrutan **Namn.**

9.  Klicka på **Lägg** till om du vill lägga till programmet.

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-single-sign-on-for-an-application-from-the-azure-ad-gallery"></a>Konfigurera enkel inloggning för ett program från Azure AD-galleriet

Så här konfigurerar du enkel inloggning för ett program:

1. <span id="_Hlk477187909" class="anchor"><span id="_Hlk477001983" class="anchor"></span></span>Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj **SAML-baserad inloggning** i listrutan **Läge.**

9. Ange de värden som krävs i **domän och webbadresser.** Du bör hämta dessa värden från programleverantören.

   1. Om du vill konfigurera programmet som SP-initierad SSO är inloggnings-URL:en ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

   2. Om du vill konfigurera programmet som IdP-initierad SSO är svars-URL:en ett obligatoriskt värde. För vissa program är identifieraren också ett obligatoriskt värde.

10. **Valfritt:** Klicka på **Visa avancerade URL-inställningar** om du vill visa de värden som inte krävs.

11. I **användarattributen**väljer du den unika identifieraren för användarna i **listrutan Användaridentifierare.**

12. **Valfritt:** Klicka på **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

13. Klicka på ** &lt;Konfigurera programnamn&gt; ** för att komma åt dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också de metadata-URL:er och certifikat som krävs för att konfigurera SSO med programmet.

14. Klicka på **Spara** om du vill spara konfigurationen.

15. Tilldela användare till programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet

Så här väljer du användaridentifieraren eller lägger till användarattribut:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program som du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Under avsnittet **Användarattribut** väljer du den unika identifieraren för användarna i listrutan **Användaridentifierare.** Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigerar alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

   2. Klicka på **Spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller -certifikatet

Så här hämtar du programmets metadata eller certifikat från Azure AD:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning visas antingen alternativet att hämta METADATA-XML:en eller certifikatet.

   Azure AD tillhandahåller ingen URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

## <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Konfigurera federerad enkel inloggning för ett program som inte är galleri

Om du vill konfigurera ett program som inte är galleri måste du ha Azure AD-premium och programmet stöder SAML 2.0. Mer information om Azure AD-versioner finns i [Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

-   Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)

-   [Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Hämta Azure AD-metadata och -certifikat](#download-the-azure-ad-metadata-or-certificate)

-   Konfigurera Azure AD-metadatavärden i programmet (Logga in på URL, Utfärdare, URL för utloggning och certifikat)

### <a name="configure-the-applications-metadata-values-in-azure-ad-sign-on-url-identifier-reply-url"></a>Konfigurera programmets metadatavärden i Azure AD (Sign on URL, Identifier, Reply URL)

Så här konfigurerar du enkel inloggning för ett program som inte finns i Azure AD-galleriet nedan:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6. Klicka på **Program för icke-galleri** i avsnittet **Lägg till din egen app.**

7. Ange namnet på programmet i textrutan **Namn.**

8. Klicka på **Lägg** till om du vill lägga till programmet.

9. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

10. Välj **SAML-baserad inloggning** i listrutan **Läge**

11. Ange de värden som krävs i **domän och webbadresser.** Du bör hämta dessa värden från programleverantören.

    1. Om du vill konfigurera programmet som IdP-initierad SSO anger du svars-URL:en och identifieraren.

    2. **Valfritt:** Om du vill konfigurera programmet som SP-initierad SSO är inloggnings-URL:en ett obligatoriskt värde.

12. I **användarattributen**väljer du den unika identifieraren för användarna i **listrutan Användaridentifierare.**

13. **Valfritt:** Klicka på **Visa och redigera alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

    Så här lägger du till ett attribut:

    1. Klicka på **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

    2. Klicka på **Spara.** Det nya attributet visas i tabellen.

14. Klicka på ** &lt;Konfigurera programnamn&gt; ** för att komma åt dokumentation om hur du konfigurerar enkel inloggning i programmet. Du har också Azure AD-URL:er och certifikat som krävs för programmet.

### <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Välj Användaridentifierare och lägg till användarattribut som ska skickas till programmet

Så här väljer du användaridentifieraren eller lägger till användarattribut:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Under avsnittet **Användarattribut** väljer du den unika identifieraren för användarna i listrutan **Användaridentifierare.** Det valda alternativet måste matcha det förväntade värdet i programmet för att autentisera användaren.

   >[!NOTE]
   >Azure AD väljer formatet för NameID-attributet (User Identifier) baserat på det valda värdet eller det format som begärs av programmet i SAML AuthRequest. Mer information finns i artikeln [Single Sign-On SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.
   >
   >

9. Om du vill lägga till användarattribut klickar du på **Visa och redigerar alla andra användarattribut** för att redigera de attribut som ska skickas till programmet i SAML-token när användare loggar in.

   Så här lägger du till ett attribut:

   1.click **Lägg till attribut**. Ange **namnet** och välj **värde** i listrutan.

   2 Klicka på **Spara.** Det nya attributet visas i tabellen.

### <a name="download-the-azure-ad-metadata-or-certificate"></a>Ladda ned Azure AD-metadata eller -certifikatet

Så här hämtar du programmets metadata eller certifikat från Azure AD:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du har konfigurerat enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Gå till avsnittet **SAML-signeringscertifikat** och klicka sedan på **Hämta** kolumnvärde. Beroende på vad programmet kräver att du konfigurerar enkel inloggning visas antingen alternativet att hämta METADATA-XML:en eller certifikatet.

   Azure AD tillhandahåller ingen URL för att hämta metadata. Metadata kan bara hämtas som en XML-fil.

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett Azure AD-galleriprogram

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   Lägga till ett program från Azure AD-galleriet

-   Konfigurera programmet för enkel inloggning för lösenord

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Lägga till ett program från Azure AD-galleriet

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Skriv namnet på programmet i textrutan **Ange ett namn** från **galleriet**

7.  Välj programmet du vill konfigurera för enkel inloggning

8.  Innan du lägger till programmet kan du ändra dess namn från textrutan **Namn.**

9.  Klicka på **Lägg** till om du vill lägga till programmet.

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj läge **Lösenordsbaserad inloggning.**

9. Tilldela användare till programmet.

10. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Konfigurera lösenordsbaserad inloggning för ett program som inte är galleri

Om du vill konfigurera ett program från Azure AD-galleriet måste du:

-   [Lägga till ett program som inte är galleri](#add-a-non-gallery-application)

-   [Konfigurera programmet för enkel inloggning för lösenord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Lägga till ett program som inte är galleri

Så här lägger du till ett program från Azure AD Gallery:

1.  Öppna [Azure-portalen](https://portal.azure.com) och logga in som **global administratör** eller **medadministratör**

2.  Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3.  Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4.  Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5.  Klicka på knappen **Lägg till** längst upp till höger i fönstret **Företagsprogram.**

6.  Klicka på **Program för icke-galleri.**

7.  Ange namnet på programmet i textrutan **Namn.** Välj **Lägg till.**

Efter en kort period kan du se programmets konfigurationsfönster.

### <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurera programmet för enkel inloggning för lösenord

Så här konfigurerar du enkel inloggning för ett program:

1. Öppna [**Azure-portalen**](https://portal.azure.com/) och logga in som **global administratör** eller **medadministratör.**

2. Öppna **Azure Active Directory-tillägget** genom att klicka på **Alla tjänster** högst upp på huvudnavigeringsmenyn.

3. Skriv in **"Azure Active Directory"** i sökrutan för filtret och välj **Azure Active Directory-objektet.**

4. Klicka på **Företagsprogram** på menyn Azure Active Directory till vänster.

5. Klicka på **Alla program om** du vill visa en lista över alla dina program.

   * Om du inte ser det program du vill visa här använder du **filterkontrollen** högst upp i **listan Alla program** och anger alternativet **Visa** till **Alla program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har läses in klickar du **på enkel inloggning** från programmets navigeringsmeny till vänster.

8. Välj läge **Lösenordsbaserad inloggning.**

9. Ange **inloggnings-URL.** Det här är webbadressen där användarna anger sitt användarnamn och lösenord för att logga in. Kontrollera att inloggningsfälten visas på webbadressen.

10. Tilldela användare till programmet.

11. Dessutom kan du också ange autentiseringsuppgifter för användaren genom att välja användarnas rader och klicka på **Uppdatera autentiseringsuppgifter** och ange användarnamn och lösenord för användarnas räkning. Annars uppmanas användarna att ange autentiseringsuppgifterna själva vid lanseringen.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Så här tilldelar du en användare till ett program direkt

Så här tilldelar du en eller flera användare ett program direkt:

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

Efter en kort period kan de användare som du har valt starta dessa program på åtkomstpanelen.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Om dessa felsökningssteg inte löser problemet

öppna en supportbiljett med följande information om sådan finns:

-   Korrelationsfel-ID

-   UPN (användarens e-postadress)

-   TenantID (Klient-ID)

-   Typ av webbläsare

-   Tidszon och tid/tidsram under fel inträffar

-   Spelman spår

## <a name="next-steps"></a>Nästa steg
[Ge enkel inloggning till dina appar med Programproxy](application-proxy-configure-single-sign-on-with-kcd.md)

