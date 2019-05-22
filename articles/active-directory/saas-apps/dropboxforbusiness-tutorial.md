---
title: 'Självstudier: Azure Active Directory-integrering med Dropbox for Business | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2109c3f67a666d0c379cc188c07968c8b31d6a1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989686"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Självstudier: Integrera Dropbox för företag med Azure Active Directory

I de här självstudierna lär du dig att integrera Dropbox för företag med Azure Active Directory (AD Azure). När du integrerar Dropbox för företag med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Dropbox för företag.
* Ge dina användare att automatiskt inloggad till Dropbox för företag med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Dropbox för företag enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

* I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Dropbox for Business har stöd för **SP**-initierad enkel inloggning

* Dropbox for Business har stöd för **just-in-time**-användaretablering

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Lägga till Dropbox for Business från galleriet

För att kunna konfigurera integreringen av Dropbox for Business i Azure AD måste du lägga till Dropbox for Business från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Dropbox for Business** i sökrutan.
1. Välj **Dropbox for Business** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Dropbox för företag med hjälp av en testanvändare kallas **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Dropbox för företag.

Om du vill konfigurera och testa Azure AD enkel inloggning med Dropbox for Business, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Dropbox för företag SSO](#configure-dropbox-for-business-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Dropbox for Business-testanvändare](#create-dropbox-for-business-test-user)** – så att det finns en motsvarighet till Britta Simon i Dropbox for Business som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Dropbox for Business** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I textrutan **Identifierare (entitets-ID)** anger du ett värde: `Dropbox`

    > [!NOTE]
    > Föregående inloggnings-URL är inte verkligt. Du uppdaterar värdet med den faktiska inloggnings-URL:en, som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Dropbox for Business** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-dropbox-for-business-sso"></a>Konfigurera Dropbox för företag SSO

1. Om du vill automatisera konfigurationen i Dropbox för företag, måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Dropbox for Business** omdirigerar dig till Dropbox för affärsprogram. Ange administratörsautentiseringsuppgifter för att logga in på Dropbox for Business därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3-8.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Dropbox for Business manuellt, öppna ett nytt webbläsarfönster och gå på din Dropbox för företag-klient och logga in på din Dropbox för företag-klient. och utför följande steg:

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurera enkel inloggning")

4. Klicka på **användarikonen** och välj fliken **Inställningar**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurera enkel inloggning")

5. Klicka på **Administratörskonsol** i den vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurera enkel inloggning")

6. I **Administratörskonsol** klickar du på **Inställningar** i det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurera enkel inloggning")

7. Välj alternativet **Enkel inloggning** under avsnittet **Autentisering**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurera enkel inloggning")

8. Gör följande i avsnittet **Enkel inloggning**:  

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurera enkel inloggning")

    a. Välj **krävs** som ett alternativ i listrutan för den **enkel inloggning**.

    b. Klicka på **Add sign-in URL** (Lägg till inloggnings-URL), och i textrutan **Identity provider sign-in URL** (Inloggnings-URL för identitetsprovider) klistrar in du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och väljer sedan **Done** (Klart).

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurera enkel inloggning")

    c. Klicka på **Ladda upp certifikat** och bläddra sedan till din **Base64-kodade certifikatfil** som du har laddat ned från Azure-portalen.

    d. Klicka på **Kopiera länk** och klistra in det kopierade värdet i textrutan **Inloggnings-URL** I avsnittet **Domän och URL:er för Dropbox for Business** i Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Dropbox för företag.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Dropbox for Business**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-dropbox-for-business-test-user"></a>Skapa Dropbox for Business-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i Dropbox for Business. Dropbox for Business stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i Dropbox for Business skapas en ny efter autentisering.

>[!Note]
>Kontakta [Dropbox for Business-kundsupporten](https://www.dropbox.com/business/contact) om du behöver skapa en användare manuellt

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer Dropbox för företag-panel i åtkomstpanelen, bör det vara loggas in automatiskt till Dropbox för företag som du konfigurerar enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)