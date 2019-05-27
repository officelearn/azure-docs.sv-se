---
title: 'Självstudier: Azure Active Directory-integrering med Proxyclick | Microsoft Docs'
description: I de här självstudierna lär du dig att konfigurera enkel inloggning mellan Azure Active Directory och Proxyclick.
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
ms.openlocfilehash: fa146ad5a36cc74a65ec6d3dee98b2ef35bc65ad
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240394"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Självstudier: Azure Active Directory-integrering med Proxyclick

I de här självstudierna lär du dig att integrera Proxyclick med Azure Active Directory (AD Azure).
Den här integrationen har följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Proxyclick.
* Du kan aktivera användarna att logga in automatiskt till Proxyclick (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure-portalen.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Proxyclick, måste du ha:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du registrera dig för en [utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/).
* En Proxyclick-prenumeration som har enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Proxyclick stöder SP-initierat och IdP-initierad SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Lägg till Proxyclick från galleriet

Om du vill konfigurera integrering av Proxyclick i Azure AD, som du behöver lägga till Proxyclick från galleriet i din lista över hanterade SaaS-appar.

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan väljer **Azure Active Directory**:

    ![Välj Azure Active Directory](common/select-azuread.png)

2. Gå till **företagsprogram** > **alla program**:

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett program, Välj **nytt program** överst i fönstret:

    ![Välj nytt program](common/add-new-app.png)

4. I sökrutan anger **Proxyclick**. Välj **Proxyclick** i sökresultatet och välj sedan **Lägg till**.

     ![Sökresultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Proxyclick med hjälp av en användare med namnet Britta Simon.
Om du vill aktivera enkel inloggning, måste du upprätta en relation mellan en Azure AD-användare och motsvarande användare i Proxyclick.

Om du vill konfigurera och testa Azure AD enkel inloggning med Proxyclick, måste du slutföra de här stegen:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  att aktivera funktionen för dina användare.
2. **[Konfigurera Proxyclick enkel inloggning](#configure-proxyclick-single-sign-on)**  på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Azure AD enkel inloggning för användaren.
5. **[Skapa en testanvändare Proxyclick](#create-a-proxyclick-test-user)**  som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen.

Konfigurera Azure AD enkel inloggning med Proxyclick genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com/), på sidan Proxyclick application integration väljer **enkel inloggning**:

    ![Välj enkel inloggning](common/select-sso.png)

2. I den **väljer du en metod för enkel inloggning** dialogrutan **SAML/WS-Fed** läge för att aktivera enkel inloggning:

    ![Välj en metod för enkel inloggning](common/select-saml-option.png)

3. På den **ange in enkel inloggning med SAML** väljer den **redigera** ikonen för att öppna den **SAML grundkonfiguration** dialogrutan:

    ![Ikonen Redigera](common/edit-urls.png)

4. I den **SAML grundkonfiguration** dialogrutan om du vill konfigurera programmet i IdP-initierad läge, vidta följande steg.

    ![Dialogrutan för grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I den **identifierare** anger en URL i det här mönstret:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. I den **svars-URL** anger en URL i det här mönstret:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Om du vill konfigurera programmet i SP-initierat läge väljer **ange ytterligare webbadresser**. I den **inloggnings-URL** anger en URL i det här mönstret:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Proxyclick domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Dessa värden är platshållare. Du måste använda det faktiska ID: t, svars-URL och inloggnings-URL. Stegen för att hämta dessa värden beskrivs senare i den här självstudien.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** väljer den **hämta** länka bredvid **certifikat (Base64)** enligt krav och spara certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

7. I den **konfigurera Proxyclick** avsnittet, kopiera de lämpliga URL: er, baserat på dina krav:

    ![Kopiera URL: er för konfiguration](common/copy-configuration-urls.png)

    1. **Inloggnings-URL**.

    1. **Azure AD-identifierare**.

    1. **URL för utloggning**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurera Proxyclick enkel inloggning

1. I ett nytt webbläsarfönster, loggar du in din Proxyclick företagets webbplats som en administratör.

2. Välj **konto och inställningar**:

    ![Välj konto och inställningar](./media/proxyclick-tutorial/configure1.png)

3. Rulla ned till den **integreringar** och väljer **SAML**:

    ![Välj SAML](./media/proxyclick-tutorial/configure2.png)

4. I den **SAML** avsnittet, vidta följande steg.

    ![SAML-avsnitt](./media/proxyclick-tutorial/configure3.png)

    1. Kopiera den **URL för SAML-konsument** värde och klistra in den i den **svars-URL** rutan den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

    1. Kopiera den **omdirigerings-URL för SAML SSO** värde och klistra in den i den **inloggnings-URL** och **identifierare** rutorna i den **grundläggande SAML-konfiguration** dialogrutan i Azure-portalen.

    1. I den **SAML begäran metoden** väljer **omdirigering för HTTP**.

    1. I den **utfärdare** rutan, klistra in den **Azure AD-identifierare** värde som du kopierade från Azure-portalen.

    1. I den **slutpunkts-URL för SAML 2.0** rutan, klistra in den **inloggnings-URL** värde som du kopierade från Azure-portalen.

    1. Öppna certifikatfilen som du laddade ned från Azure-portalen i anteckningar. Klistra in innehållet i filen till den **certifikat** box.

    1. Välj **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** i den vänstra rutan väljer **användare**, och välj sedan **alla användare**:

    ![Välj alla användare](common/users.png)

2. Välj **ny användare** överst på skärmen:

    ![Välj ny användare](common/new-user.png)

3. I den **användaren** dialogrutan rutan, vidta följande steg.

    ![Användardialogrutan](common/user-properties.png)

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **BrittaSimon @\<företagsdomänen >.\< tillägget >** . (Till exempel BrittaSimon@contoso.com.)

    1. Välj **visa lösenord**, och sedan skriva ned det värde som är i den **lösenord** box.

    1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Proxyclick.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**, och välj sedan **Proxyclick**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Proxyclick**.

    ![Lista över program](common/all-applications.png)

3. I den vänstra rutan väljer **användare och grupper**:

    ![Välj Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i listan och klicka sedan på den **Välj** knappen längst ned i fönstret.

6. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** dialogrutan väljer du rätt roll för användaren i listan. Klicka på den **Välj** knappen längst ned i fönstret.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-proxyclick-test-user"></a>Skapa en Proxyclick testanvändare

Om du vill aktivera Azure AD-användare att logga in på Proxyclick som du behöver lägga till dem i Proxyclick. Du måste lägga till dem manuellt.

Skapa ett användarkonto genom att göra följande:

1. Logga in på webbplatsen Proxyclick företag som en administratör.

1. Välj **kollegor** överst i fönstret:

    ![Välj kollegor](./media/proxyclick-tutorial/user1.png)

1. Välj **lägga till kollega**:

    ![Välj Lägg till kollega](./media/proxyclick-tutorial/user2.png)

1. I den **lägga till en kollega** avsnittet, vidta följande steg.

    ![Lägg till en kollega-avsnitt](./media/proxyclick-tutorial/user3.png)

    1. I den **e-post** anger du e-postadressen för användaren. I det här fallet **brittasimon\@contoso.com**.

    1. I den **Förnamn** anger du det första namnet för användaren. I det här fallet **Britta**.

    1. I den **efternamn** anger efternamn för användaren. I det här fallet **Simon**.

    1. Välj **Lägg till användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

Nu ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Proxyclick i åtkomstpanelen, bör det vara loggas in automatiskt till Proxyclick-instansen som du ställer in enkel inloggning. Läs mer om åtkomstpanelen [öppna och använda appar på portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

