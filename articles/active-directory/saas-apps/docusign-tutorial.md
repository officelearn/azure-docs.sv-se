---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med DocuSign | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01f969c3bc6f546025b3bbe5826181efdfa69be0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983669"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Microsoft Azure Active Directory (Azure AD). När du integrerar DocuSign med Azure AD kan du:

* Använd Azure AD för att styra vem som har åtkomst till DocuSign.
* Aktivera automatisk inloggning till DocuSign för dina användare via deras Azure AD-konton.
* Hantera dina konton på en central plats: Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns [i Enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En DocuSign-prenumeration som är enkel inloggning (SSO) aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö för att verifiera att:

* DocuSign stöder serviceprovider (SP)-initierad SSO.

* DocuSign stöder *just-in-time-användaretablering.*

* DocuSign stöder [automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial).
* När du har konfigurerat DocuSign kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-docusign-from-the-gallery"></a>Lägga till DocuSign från galleriet

Om du vill konfigurera integreringen av DocuSign i Azure AD måste du lägga till DocuSign från galleriet i listan över hanterade SaaS-appar:

1. Logga in på [Azure-portalen](https://portal.azure.com) med hjälp av ett arbets- eller skolkonto eller med hjälp av ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i navigeringsfönstret till vänster.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **DocuSign** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **DocuSign** på resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Konfigurera och testa en azure AD-inloggning för DocuSign

Konfigurera och testa Azure AD SSO med DocuSign med hjälp av en testanvändare med namnet **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och motsvarande användare i DocuSign.

Så här konfigurerar och testar du Azure AD SSO med DocuSign:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD enkel inloggning med B.Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) för att aktivera B.Simon att använda Azure AD enkel inloggning.
1. [Konfigurera DocuSign SSO](#configure-docusign-sso) för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. [Skapa en DocuSign-testanvändare](#create-docusign-test-user) för att generera en motsvarighet till B.Simon i DocuSign som är länkad till Azure AD-representationen för användaren.
1. [Testa SSO](#test-sso) för att kontrollera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO i Azure-portalen:

1. Leta reda på avsnittet Hantera på sidan **Hantera** på sidan [Azure-portalen](https://portal.azure.com/)och välj sedan **enkel inloggning**. **Manage**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Gör så här i avsnittet **Grundläggande SAML-konfiguration:**

    a. Ange en WEBBADRESS i rutan **Logga in på WEBBADRESS** med följande mönster:`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. Ange en URL med följande mönster i rutan **Identifierare (enhets-ID):**`https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dessa parentesvärden är platshållare. Ersätt dem med värdena i den faktiska inloggnings-URL:en och identifieraren. Dessa uppgifter förklaras i avsnittet "Visa SAML 2.0-slutpunkter" senare i den här självstudien.

1. Sök upp **certifikat (Base64)** i avsnittet **SAML Signering Certificate** på sidan Konfigurera enkel inloggning med **SAML.** Välj **Hämta** om du vill hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera DocuSign** kopierar du lämplig URL (eller webbadresser) baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet B.Simon i Azure-portalen.

1. I den vänstra rutan i Azure-portalen väljer du **Azure Active Directory**, väljer **Användare**och väljer sedan **Alla användare**.
1. Högst upp på skärmen väljer du **Ny användare**.
1. Gör så här i egenskaperna **Användare:**
   1. Ange **B.Simon**i fältet **Namn** .  
   1. Ange `<username>@<companydomain>.<extension>`i fältet **Användarnamn** . Till exempel: `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och notera sedan värdet som visas i rutan **Lösenord.**
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du ge B.Simon åtkomst till DocuSign så att den här användaren kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **DocuSign**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan **Användare** och trycker sedan på **knappen Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och trycker sedan på **knappen Välj** längst ned på skärmen.
1. Välj knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-docusign-sso"></a>Konfigurera DocuSign SSO

1. Om du vill automatisera konfigurationen i DocuSign måste du installera webbläsartillägget Installera säker inloggning genom att välja **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren väljer du **Konfigurera DocuSign**. Du dirigeras till DocuSign-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på DocuSign. Webbläsartillägget konfigurerar automatiskt programmet och automatiserar steg 3 till 5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera DocuSign manuellt öppnar du ett nytt webbläsarfönster och loggar in på docusign-företagets webbplats som administratör.

4. I det övre högra hörnet på sidan markerar du profillogotypen och väljer sedan **Gå till Admin**.
  
    ![Gå till Admin under Profil][51]

5. Välj Domäner på sidan **domänlösningar**.

    ![Domänlösningar/domäner][50]

6. Välj **ANSPRÅKSDOMÄN I**avsnittet **Domäner** .

    ![Alternativet Anspråksdomän][52]

7. Skriv företagsdomänen i rutan **Domännamn** i dialogrutan **Begär en domän** och välj sedan **ANSPRÅK**. Kontrollera att du verifierar domänen och att dess status är aktiv.

    ![Göra anspråk på dialogrutan Domännamn][53]

8. På sidan domänlösningar väljer du **Identitetsleverantörer**.
  
    ![Alternativ för identitetsleverantörer][54]

9. I avsnittet **Identitetsleverantörer** väljer du **ADD IDENTITY PROVIDER**.

    ![Alternativet Lägg till identitetsprovider][55]

10. Gör så här på sidan **Inställningar för identitetsprovider:**

    ![Inställningar för identitetsprovider][56]

    a. Skriv ett unikt namn för konfigurationen i rutan **Namn.** Använd inte mellanslag.

    b. Klistra in azure **AD-identifierare** i **rutan Identitetsproviderutfärdare,** som du kopierade från Azure-portalen.

    c. I rutan **Url för inloggningsadress för identitetsprovider** klistrar du in värdet **för inloggningsadressen,** som du kopierade från Azure-portalen.

    d. I **url:en för identitetsproviderloggning** klistrar du in värdet **för url för utloggning**, som du kopierade från Azure-portalen.

    e. Välj **Sign AuthN-begäran**.

    f. För **Skicka AuthN-begäran av**väljer du **POST**.

    g. För **Skicka utloggningsbegäran av**väljer du **GET**.

    h. I avsnittet **Anpassad attributmappning** väljer du **LÄGG TILL NY MAPPNING**.

       ![Användargränssnitt för anpassad attributmappning][62]

    i. Välj det fält som du vill mappa till Azure AD-anspråket. I det här exemplet mappas **anspråket för e-postadress** med värdet för `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`. Det är standardanspråksnamnet från Azure AD för e-postanspråket. Välj **SPARA**.

       ![Fält för anpassad attributmappning][57]

       > [!NOTE]
       > Använd lämplig **användaridentifierare** för att mappa användaren från Azure AD till DocuSign-användarmappning. Välj rätt fält och ange lämpligt värde baserat på organisationens inställningar.

    j. I avsnittet **Identitetsprovidercertifikat** väljer du **LÄGG TILL CERTIFIKAT**, överför certifikatet som du hämtade från Azure AD-portalen och väljer **SPARA**.

       ![Identitetsprovidercertifikat/Lägg till certifikat][58]

    k. I avsnittet **Identitetsprovider** väljer du **ÅTGÄRDER**och väljer sedan **Slutpunkter**.

       ![Identitetsleverantörer/slutpunkter][59]

    l. Gör så här i avsnittet **Visa SAML 2.0-slutpunkter** i DocuSign-administratörsportalen:
       1. Kopiera **URL:en för serviceproviderutfärdaren**och klistra sedan in den i rutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

       1. Kopiera **tjänstens inloggningsadress**och klistra sedan in den i rutan Logga in **url** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

       1. Välj **Stäng**.

       ![Visa SAML 2.0-slutpunkter][60]

### <a name="create-docusign-test-user"></a>Skapa DocuSign-testanvändare

I det här avsnittet skapas en användare med namnet B.Simon i DocuSign. DocuSign stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i DocuSign skapas en ny efter autentisering.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [DocuSign-supportteamet](https://support.docusign.com/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen DocuSign på åtkomstpanelen bör du automatiskt loggas in på den DocuSign-instans som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning i Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova DocuSign med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png