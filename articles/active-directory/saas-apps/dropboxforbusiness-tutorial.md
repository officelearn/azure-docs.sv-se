---
title: 'Självstudie: Azure Active Directory integrering med Dropbox för företag | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf5b3c79e39e9baef49ce2c14408a15c3686a6a8
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293444"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Självstudie: integrera Dropbox för företag med Azure Active Directory

I den här självstudien får du lära dig att integrera Dropbox för företag med Azure Active Directory (Azure AD). När du integrerar Dropbox för företag med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Dropbox för företag.
* Gör det möjligt för användarna att logga in automatiskt till Dropbox för företag med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Dropbox for Business Single Sign-on (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

* I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Dropbox for Business har stöd för **SP**-initierad enkel inloggning

* Dropbox for Business har stöd för [Automatisk användar etablering och avetablering](dropboxforbusiness-tutorial.md)
* När du har konfigurerat Dropbox kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Lägga till Dropbox for Business från galleriet

För att kunna konfigurera integreringen av Dropbox for Business i Azure AD måste du lägga till Dropbox for Business från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Dropbox for Business** i sökrutan.
1. Välj **Dropbox för företag** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Dropbox för företag med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Dropbox för företag.

Slutför följande Bygg stenar om du vill konfigurera och testa Azure AD SSO med Dropbox för företag:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.    
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Dropbox for Business SSO](#configure-dropbox-for-business-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Dropbox for Business-testanvändare](#create-dropbox-for-business-test-user)** – så att det finns en motsvarighet till Britta Simon i Dropbox for Business som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Dropbox for Business** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I textrutan **Identifierare (entitets-ID)** anger du ett värde: `Dropbox`

    > [!NOTE]
    > Föregående inloggnings-URL är inte verkligt. Du uppdaterar värdet med den faktiska inloggnings-URL:en, som förklaras senare i självstudien.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Dropbox for Business** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Dropbox för företag.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Dropbox för företag**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-dropbox-for-business-sso"></a>Konfigurera Dropbox för Business SSO

1. Om du vill automatisera konfigurationen i Dropbox för företag måste du installera **tillägget Mina appar säker inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren dirigeras du till Dropbox for Business-appen genom att klicka på **Installera Dropbox för företag** . Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Dropbox för företag. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill installera Dropbox för företag manuellt öppnar du ett nytt webbläsarfönster och går till Dropbox för företag-klienten och loggar in på Dropbox för företag-klienten. och utför följande steg:

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

    a. Välj **obligatoriskt** som ett alternativ i list rutan för **enkel inloggning**.

    b. Klicka på **Add sign-in URL** (Lägg till inloggnings-URL), och i textrutan **Identity provider sign-in URL** (Inloggnings-URL för identitetsprovider) klistrar in du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och väljer sedan **Done** (Klart).

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurera enkel inloggning")

    c. Klicka på **Ladda upp certifikat** och bläddra sedan till din **Base64-kodade certifikatfil** som du har laddat ned från Azure-portalen.

    d. Klicka på **Kopiera länk** och klistra in det kopierade värdet i textrutan **Inloggnings-URL** I avsnittet **Domän och URL:er för Dropbox for Business** i Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-dropbox-for-business-test-user"></a>Skapa Dropbox for Business-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i Dropbox for Business. Dropbox for Business stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i Dropbox for Business skapas en ny efter autentisering.

>[!Note]
>Kontakta [Dropbox for Business-kundsupporten](https://www.dropbox.com/business/contact) om du behöver skapa en användare manuellt

### <a name="test-sso"></a>Testa SSO

När du väljer ikonen Dropbox för företag på åtkomst panelen, bör du loggas in automatiskt till Dropbox för företag som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/cloud-app-security/protect-dropbox)

- [Skydda Dropbox med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
