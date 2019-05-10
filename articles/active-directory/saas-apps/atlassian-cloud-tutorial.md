---
title: 'Självstudier: Azure Active Directory-integrering med Atlassian Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassian Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66b8b9076c0a4b1fddda4ab0bcfe9f104d7dcf8a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65191130"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Självstudier: Azure Active Directory-integrering med Atlassian Cloud

I den här självstudiekursen lär du dig hur du integrerar Atlassian Cloud med Azure Active Directory (Azure AD).
När du integrerar Atlassian Cloud med Azure AD innebär det följande fördelar:

* Du kan styra i Azure AD vilka som har åtkomst till Atlassian Cloud.
* Du kan göra så att dina användare automatiskt loggas in på Atlassian Cloud (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med Atlassian Cloud behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Atlassian Cloud-prenumeration med enkel inloggning aktiverat
* Om du vill aktivera enkel inloggning med SAML för Atlassian Cloud-produkter måste du konfigurera Atlassian-åtkomst. Läs mer om [Atlassians-åtkomst]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Atlassian Cloud stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Lägga till Atlassian Cloud från galleriet

Om du vill konfigurera integreringen av Atlassian Cloud i Azure AD måste du lägga till Atlassian Cloud från galleriet till din lista över hanterade SaaS-appar.

**Lägg till Atlassian Cloud från galleriet genom att göra följande:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Atlassian Cloud** i sökrutan, välj **Atlassian Cloud** på resultatpanelen och lägg sedan till programmet genom att klicka på knappen **Lägg till**.

     ![Atlassian Cloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Atlassian Cloud baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Atlassian Cloud upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Atlassian Cloud måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Atlassian Cloud](#configure-atlassian-cloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa en testanvändare för Atlassian Cloud](#create-atlassian-cloud-test-user)**  – så att du får en motsvarighet till Britta Simon i Atlassian Cloud som är länkad till användarens Azure AD-representation.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning i Azure AD med Atlassian Cloud genom att göra följande:

1. Välj **Enkel inloggning** på programintegreringssidan för **Atlassian Cloud** i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Information om [Programnamn]-domän och URL:er med enkel inloggning](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://auth.atlassian.com/saml/<unique ID>`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Föregående värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en. Du får dessa verkliga värden från den **Atlassians Cloud SAML-konfiguration** skärmen som beskrivs senare i den **konfigurera Atlassians molnet enkel inloggning** av kursen.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om [Programnamn]-domän och URL:er med enkel inloggning](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Föregående inloggnings-URL-värde är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta det här värdet genom att kontakta [supportteamet för Atlassians Cloud-klienten](https://support.atlassian.com/).

6. Ditt Atlassian Cloud-program förväntar sig SAML-intygen i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Atlassian Cloud-programmet förväntar sig att **nameidentifier** mappas med **user.mail**, så du behöver redigera attributmappningen genom att klicka på ikonen **Redigera** och ändra attributmappningen.

    ![image](common/edit-attribute.png)

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera Atlassian Cloud** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-atlassian-cloud-single-sign-on"></a>Konfigurera enkel inloggning för Atlassian Cloud

1. Om du vill automatisera konfigurationen inom Atlassians moln, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Atlassians molnet** omdirigerar dig till Atlassians molnprogram. Ange administratörsautentiseringsuppgifter för att logga in på molnet Atlassians därifrån. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Atlassians molnet manuellt, öppna ett nytt webbläsarfönster och logga till Atlassians molnet företagets webbplatsen som administratör och utför följande steg:

4. Du måste verifiera din domän innan du fortsätter med att konfigurera enkel inloggning. Mer information finns i dokumentet [Atlassian-domänverifiering](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).

5. Välj **Enkel SAML-inloggning** i den vänstra rutan. Prenumerera på Atlassian Identity Manager om du inte redan gör det.

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

6. Gör följande i fönstret **Lägg till SAML-konfiguration**:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. I den **identitetsprovider entitets-ID** rutan, klistra in den **Azure AD-identifierare** som du kopierade från Azure-portalen.

    b. I den **identitetsprovider SSO URL** rutan, klistra in den **inloggnings-URL** som du kopierade från Azure-portalen.

    c. Öppna det nedladdade certifikatet från Azure Portal i en txt-fil, kopiera värdet (utan raderna *Starta certifikat* och *Avsluta certifikat*) och klistra in det i rutan **Offentligt X509-certifikat**.

    d. Klicka på **Spara konfiguration**.

7. Säkerställ att du har konfigurerat rätt URL:er genom att uppdatera Azure AD-inställningarna på följande sätt:

    ![Konfigurera enkel inloggning](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. I fönstret SAML kopiera den **SP identitets-ID** och sedan i Azure-portalen under Atlassians molnet **grundkonfiguration SAML**, klistra in den i den **identifierare** box.

    b. I fönstret SAML kopiera den **SP URL för Konsumenttjänst för försäkran** och sedan i Azure-portalen under Atlassians molnet **SAML grundkonfiguration**, klistra in den i den **svars-URL**box. Inloggnings-URL:en är klient-URL:en för ditt Atlassian-moln.

    > [!NOTE]
    > Välj **Ja, uppdatera konfigurationen**, om du är en befintlig kund, när du har uppdaterat värdena för **SP-identitets-ID** och **URL för SP-konsumenttjänst för försäkran** i Azure Portal. Om du är en ny kund kan du hoppa över det här steget.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet får du aktivera Britta Simon till att använda enkel inloggning i Azure genom att bevilja henne åtkomst till Atlassian Cloud.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Atlassian Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Atlassian Cloud** i programlistan.

    ![Atlassian Cloud-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-atlassian-cloud-test-user"></a>Skapa testanvändare för Atlassian Cloud

Om du vill aktivera Azure AD-användare så att de kan logga in på Atlassian Cloud måste du etablera användarkontona manuellt i Atlassian Cloud genom att göra följande:

1. Välj **Användare** på **Administration**-panelen.

    ![Atlassian Cloud-användarlänken](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Välj **Bjud in användare** om du vill skapa en användare i Atlassian Cloud.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Ange användarens e-postadress i rutan **E-postadress** och tilldela sedan programmet åtkomst.

    ![Skapa en Atlassian Cloud-användare](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Välj **Bjud in användare** om du vill skicka en e-postinbjudan till användaren. En e-postinbjudan skickas till användaren och efter det att användaren har accepterat inbjudan aktiveras hen i systemet.

> [!NOTE]
> Du kan också skapa flera användare genom att använda knappen **Skapa flera** i avsnittet **Användare**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Atlassian Cloud-panelen i åtkomstpanelen bör du automatiskt loggas in till Atlassian Cloud som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   
