---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med DocuSign | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306300"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Azure Active Directory (Azure AD). När du integrerar DocuSign med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till DocuSign.
* Gör det möjligt för användarna att logga in automatiskt till DocuSign med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* DocuSign för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* DocuSign stöder **SP** -INITIERAd SSO

* DocuSign stöder **just-in-Time-** etablering av användare

* DocuSign stöder [Automatisk användar etablering](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Lägga till DocuSign från galleriet

Om du vill konfigurera integreringen av DocuSign i Azure AD måste du lägga till DocuSign från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **DocuSign** i sökrutan.
1. Välj **DocuSign** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Konfigurera och testa enkel inloggning med Azure AD för DocuSign

Konfigurera och testa Azure AD SSO med DocuSign med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i DocuSign.

Om du vill konfigurera och testa Azure AD SSO med DocuSign, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera DOCUSIGN SSO](#configure-docusign-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa DocuSign test User](#create-docusign-test-user)** -om du vill ha en motsvarighet till B. Simon i DocuSign som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **DocuSign** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och identifieraren som förklaras senare **Visa SAML 2,0-slut punkter** i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera DocuSign** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till DocuSign.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **DocuSign**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-docusign-sso"></a>Konfigurera DocuSign SSO

1. Om du vill automatisera konfigurationen i DocuSign måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations DocuSign** för att dirigera dig till DocuSign-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på DocuSign. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera DocuSign manuellt öppnar du ett nytt webbläsarfönster och loggar in på din DocuSign-företags webbplats som administratör och utför följande steg:

4. Längst upp till höger på sidan klickar du på profil **logo typ** och sedan på **gå till administratör**.
  
    ![Konfigurera enkel inloggning][51]

5. På sidan domän lösningar klickar du på **domäner**

    ![Konfigurera enkel inloggning][50]

6. Under avsnittet **domäner** klickar du på **anspråks domän**.

    ![Konfigurera enkel inloggning][52]

7. Skriv din företags domän i text rutan **domän namn** i dialog rutan **anspråk en domän** och klicka sedan på **anspråk**. Kontrol lera att du verifierar domänen och att statusen är aktiv.

    ![Konfigurera enkel inloggning][53]

8. Klicka på **identitets leverantörer**på sidan domän lösningar.
  
    ![Konfigurera enkel inloggning][54]

9. Under avsnittet **identitets leverantörer** klickar du på **Lägg till identitets leverantör**. 

    ![Konfigurera enkel inloggning][55]

10. Utför följande steg på sidan **Inställningar för identitetsprovider** :

    ![Konfigurera enkel inloggning][56]

    a. I text rutan **namn** anger du ett unikt namn för din konfiguration. Använd inte blank steg.

    b. I **text rutan för identitets leverantörs utfärdare**klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    c. I text rutan **inloggnings-URL för identitetsprovider** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    d. I text rutan **URL för identitets leverantörs utloggning** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    e. Välj **signera authn-begäran**.

    f. Som **Skicka authn-begäran av**väljer du **post**.

    g. Som **Skicka utloggnings förfrågan efter**väljer du **Hämta**.

    h. I avsnittet **Mappning av anpassade attribut** klickar du på **Lägg till ny mappning**.

    ![Konfigurera enkel inloggning][62]

    i. Välj det fält som du vill mappa med Azure AD-anspråk. I det här exemplet mappas **EmailAddress** -anspråket till värdet **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Det är standard anspråks namnet från Azure AD för e-postanspråk och klicka sedan på **Spara**.

    ![Konfigurera enkel inloggning][57]

    > [!NOTE]
    > Använd rätt **användar identifierare** för att mappa användaren från Azure AD till DocuSign användar mappning. Välj fältet korrekt och ange lämpligt värde baserat på organisations inställningarna.

    j. I avsnittet **certifikat för identitetsprovider** klickar du på **Lägg till certifikat**och laddar sedan upp det certifikat som du har laddat ned från Azure AD Portal och klickar på **Spara**.

    ![Konfigurera enkel inloggning][58]

    k. I avsnittet **identitets leverantörer** klickar du på **åtgärder**och sedan på **slut punkter**.

    ![Konfigurera enkel inloggning][59]

    l. I avsnittet **Visa SAML 2,0-slutpunkter** på **DocuSign Admin Portal**, utför följande steg:

    ![Konfigurera enkel inloggning][60]

    * Kopiera **URL: en för service providerns utfärdare**och klistra in den i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    * Kopiera **inloggnings-URL: en för tjänst leverantören**och klistra in den i text rutan för **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.

    * Klicka på **Stäng**

### <a name="create-docusign-test-user"></a>Skapa DocuSign test användare

I det här avsnittet skapas en användare som heter B. Simon i DocuSign. DocuSign stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i DocuSign skapas en ny efter autentiseringen.

>[!Note]
>Kontakta [DocuSign support team](https://support.docusign.com/)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DocuSign på åtkomst panelen, bör du loggas in automatiskt på den DocuSign som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova DocuSign med Azure AD](https://aad.portal.azure.com/)

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