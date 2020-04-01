---
title: 'Självstudiekurs: Azure Active Directory-integrering med Proxyclick | Microsoft-dokument'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093497"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudiekurs: Azure Active Directory-integrering med Proxyclick

I den här självstudien får du lära dig hur du integrerar Proxyclick med Azure Active Directory (Azure AD).
Den här integrationen ger följande fördelar:

* Du kan använda Azure AD för att styra vem som har åtkomst till Proxyclick.
* Du kan aktivera dina användare så att de automatiskt loggas in på Proxyclick (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Proxyclick måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [månads utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).
* En Proxyclick-prenumeration som har enkel inloggning aktiverad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD enkel inloggning i en testmiljö.

* Proxyclick stöder SP-initierad och IdP-initierad SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Om du vill konfigurera integreringen av Proxyclick i Azure AD måste du lägga till Proxyclick från galleriet i listan över hanterade SaaS-appar.

1. Välj **Azure Active Directory**i [Azure-portalen](https://portal.azure.com)i den vänstra rutan:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **Enterprise-program** > **Alla program:**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program väljer du **Nytt program** högst upp i fönstret:

    ![Välj Nytt program](common/add-new-app.png)

4. Ange **Proxyclick**i sökrutan . Välj **Proxyclick** i sökresultaten och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Proxyclick med hjälp av en testanvändare som heter Britta Simon.
Om du vill aktivera enkel inloggning måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Proxyclick.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med Proxyclick måste du utföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** för att aktivera funktionen för dina användare.
2. **[Konfigurera Proxyclick enkel inloggning på](#configure-proxyclick-single-sign-on)** programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera azure AD-enkel inloggning för användaren.
5. **[Skapa en Proxyclick-testanvändare](#create-a-proxyclick-test-user)** som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** för att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel Azure AD-inloggning i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Proxyclick:

1. Välj Enkel inloggning på sidan Proxyclick-programintegrering i [Azure-portalen](https://portal.azure.com/): **Single sign-on**

    ![Välj enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en enda inloggningsmetod** väljer du **SAML/WS-Fed-läge** för att aktivera enkel inloggning:

    ![Välj en enda inloggningsmetod](common/select-saml-option.png)

3. På sidan Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration:** **Set up Single Sign-On with SAML**

    ![Redigera-ikonen](common/edit-urls.png)

4. Om du vill konfigurera programmet i IdP-initierat läge i dialogrutan Grundläggande SAML-konfiguration gör du följande i dialogrutan **Grundläggande SAML-konfiguration.**

    ![Dialogrutan Grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. Ange en URL i det här mönstret i rutan **Identifierare:**
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Ange en URL i det här mönstret i rutan **Svara url:**

       `https://saml.proxyclick.com/consume/<companyId>`

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare webbadresser**. Ange en WEBBADRESS i det här mönstret i rutan **Logga in på webbadress:**
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick Domän och webbadresser enkel inloggningsinformation](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda den faktiska identifieraren, svars-URL:en och inloggnings-URL:en. Steg för att hämta dessa värden beskrivs senare i den här självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** väljer du länken **Hämta bredvid** **Certifikat (Base64)** enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

7. I avsnittet **Konfigurera proxyclick** kopierar du lämpliga webbadresser baserat på dina krav:

    ![Kopiera konfigurationsadresserna](common/copy-configuration-urls.png)

    1. **Inloggningsadress**.

    1. **Azure AD-identifierare**.

    1. **Url för utloggning**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurera Proxyclick enkel inloggning

1. I ett nytt webbläsarfönster loggar du in på din Proxyclick-företagswebbplats som administratör.

2. Välj **inställningar för konto &:**

    ![Välj inställningar för konto &](./media/proxyclick-tutorial/configure1.png)

3. Bläddra ned till avsnittet **Integrationer** och välj **SAML:**

    ![Välj SAML](./media/proxyclick-tutorial/configure2.png)

4. I avsnittet **SAML** gör du följande steg:

    ![SAML-sektion](./media/proxyclick-tutorial/configure3.png)

    1. Kopiera **URL-värdet för SAML-konsumenter** och klistra in det i rutan **Svars-URL** i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. Kopiera **URL-värdet för SAML SSO-omdirigering** och klistra in det i **rutorna Logga in på URL** och **Identifierare** i dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen.

    1. Välj **HTTP-omdirigering**i listan **SAML-begärandemetod** .

    1. I rutan **Utfärdare** klistrar du in **azure AD-identifierare** som du kopierade från Azure-portalen.

    1. I **url-rutan SAML 2.0** klistrar du in värdet **för inloggnings-URL** som du kopierade från Azure-portalen.

    1. Öppna certifikatfilen som du hämtade från Azure-portalen i Anteckningar. Klistra in innehållet i den här filen i rutan **Certifikat.**

    1. Välj **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan, väljer **Användare**och väljer sedan **Alla användare:**

    ![Välj alla användare](common/users.png)

2. Välj **Ny användare** högst upp på skärmen:

    ![Välj Ny användare](common/new-user.png)

3. Gör följande i dialogrutan **Användare.**

    ![Dialogrutan Användare](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **Användarnamn** anger du **BrittaSimon@\<ditt företag>.\< förlängning>**. (Till exempel BrittaSimon@contoso.com.)

    1. Välj **Visa lösenord**och skriv sedan ned värdet i rutan **Lösenord.**

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda Azure enkel inloggning genom att ge henne åtkomst till Proxyclick.

1. I Azure-portalen väljer du **Enterprise-program**, väljer **Alla program**och väljer sedan **Proxyclick**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Proxyclick**i listan över program .

    ![Lista över ansökningar](common/all-applications.png)

3. Välj Användare och **grupper**i den vänstra rutan:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i användarlistan och klickar sedan på knappen **Välj** längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML- påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll.** Klicka på knappen **Välj** längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-proxyclick-test-user"></a>Skapa en Proxyclick-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på Proxyclick måste du lägga till dem i Proxyclick. Du måste lägga till dem manuellt.

Så här skapar du ett användarkonto:

1. Logga in på din Proxyclick-företagswebbplats som administratör.

1. Välj **Kollegor** högst upp i fönstret:

    ![Välj kollegor](./media/proxyclick-tutorial/user1.png)

1. Välj **Lägg till kollega:**

    ![Välj Lägg till kollega](./media/proxyclick-tutorial/user2.png)

1. Gör följande i avsnittet **Lägg till en kollega.**

    ![Lägga till ett kollegaavsnitt](./media/proxyclick-tutorial/user3.png)

    1. Ange användarens e-postadress i rutan **E-post.** I det här fallet **contoso.com brittasimon\@**.

    1. Ange användarens förnamn i rutan **Förnamn.** I det här **fallet, Britta**.

    1. Ange användarens efternamn i rutan **Efternamn.** I det här fallet, **Simon.**

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu måste du testa din Azure AD-konfiguration med enkel inloggning med hjälp av åtkomstpanelen.

När du väljer panelen Proxyclick på åtkomstpanelen bör du automatiskt loggas in i proxyclick-instansen som du ställer in SSO för. Mer information om Åtkomstpanelen finns i [Komma till åtkomst och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

