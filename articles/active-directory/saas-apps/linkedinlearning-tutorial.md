---
title: 'Självstudiekurs: Integrering av Enkel inloggning i Azure Active Directory (SSO) med LinkedIn Learning | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Självstudiekurs: Integrering av Enkel inloggning i Azure Active Directory (SSO) med LinkedIn Learning

I den här självstudien får du lära dig hur du integrerar LinkedIn Learning med Azure Active Directory (Azure AD). När du integrerar LinkedIn Learning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LinkedIn Learning.
* Gör att användarna automatiskt loggas in på LinkedIn Learning med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* En SSO-prenumeration (LinkedIn Learning single sign-on).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* LinkedIn Learning stöder **SP- och IDP**-initierad enkel inloggning
* LinkedIn Learning stöder **just-in-time**-användaretablering
* När du har konfigurerat LinkedIn Learning kan du framtvinga sessionskontroll, som skyddar exfiltrering och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>Lägga till LinkedIn Learning från galleriet

När du ska konfigurera integrering av LinkedIn Learning i Azure AD måste du lägga till LinkedIn Learning från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **LinkedIn Learning** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **LinkedIn Learning** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Konfigurera och testa en azure AD-inloggning för LinkedIn Learning

Konfigurera och testa Azure AD SSO med LinkedIn Learning med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i LinkedIn Learning.

Om du vill konfigurera och testa Azure AD SSO med LinkedIn Learning slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera LinkedIn Learning SSO](#configure-linkedin-learning-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa LinkedIn Learning-testanvändare](#create-linkedin-learning-test-user)** – om du vill ha en motsvarighet till B.Simon i LinkedIn Learning som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **LinkedIn Learning-program** i [Azure-portalen](https://portal.azure.com/)och välj **enkel inloggning**.
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

     a. Ange det **entitets-ID** som kopierats från LinkedIn-portalen i textrutan **Identifierare**. 

    b. Ange den **ACS-URL** som kopierats från LinkedIn-portalen i textrutan **Svars-URL**.

    c. Om du vill konfigurera programmet i **SP-initierat** läge klickar du sedan på alternativet **Ange ytterligare URL:er** i avsnittet för den **Grundläggande SAML-konfiguration** där du ska ange din inloggnings-URL. Skapa din inloggnings-URL genom att kopiera **ACS-URL:en** och ersätta /saml/ med /login/. När du har gjort det ska inloggnings-URL:en ha följande mönster:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Information om LinkedIn Learning-domän och URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga värden. Du kommer att uppdatera dessa värden med den faktiska url:en för identifierare och svar, som förklaras senare i avsnittet **Konfigurera LinkedIn Learning SSO** i självstudiekursen.

1. LinkedIn Learning-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. LinkedIn Learning-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera LinkedIn Learning** baserat på dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till LinkedIn Learning.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **LinkedIn Learning**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-linkedin-learning-sso"></a>Konfigurera LinkedIn Learning SSO

1. Logga in till din LinkedIn Learning-klient som administratör i ett annat webbläsarfönster.

2. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Markera dessutom **Inlärning – standard** i den nedrullningsbara listan.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klicka på ** ELLER klicka här om du vill läsa in och kopiera enskilda fält i formuläret** och kopiera **entitets-Id** och **ACS-URL** och klistra in dem i avsnittet ** Grundläggande SAML-konfiguration** i Azure Portal.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Gå till avsnittet **LinkedIn-administratörsinställningar**. Överför den XML-fil du laddade ned från Azure Portal genom att klicka på alternativet **Ladda upp XML-fil**.

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Aktivera enkel inloggning genom att klicka på **På**. SSO-statusen ändras från **Inte ansluten** till **Ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Skapa en testanvändare för LinkedIn Learning

LinkedIn Learning-programmet stöder just-in-time-användaretablering och efter autentiseringen skapas användare automatiskt i programmet. På LinkedIn Learning-portalens sida för administratörsinställningar ändrar du växeln **Tilldela licenser automatiskt** till aktiv just-in-time-etablering, varvid använderen även tilldelas en licens.

   ![Skapa en testanvändare för Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på LinkedIn Learning i åtkomstpanelen bör du bli automatiskt inloggad på den LinkedIn Learning som du konfigurerade enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova LinkedIn Learning med Azure AD](https://aad.portal.azure.com/)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)