---
title: 'Självstudier: Azure Active Directory-integrering med Dropbox for Business | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dropbox for Business.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: a05a246a3bdf0594484cbbf89998e337d2be5a12
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974974"
---
# <a name="tutorial-azure-active-directory-integration-with-dropbox-for-business"></a>Självstudier: Azure Active Directory-integrering med Dropbox for Business

I den här självstudien lär du dig hur du integrerar Dropbox for Business med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Dropbox for Business med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Dropbox for Business.
* Du kan låta dina användare loggas in automatiskt på Dropbox for Business (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure Portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med Dropbox for Business behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Dropbox for Business-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Dropbox for Business har stöd för **SP**-initierad enkel inloggning

* Dropbox for Business har stöd för **just-in-time**-användaretablering

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Lägga till Dropbox for Business från galleriet

För att kunna konfigurera integreringen av Dropbox for Business i Azure AD måste du lägga till Dropbox for Business från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Dropbox for Business från galleriet:**

1. I **[Azure Portal](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Dropbox for Business** i sökrutan, välj **Dropbox for Business** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

     ![Dropbox for Business i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Dropbox for Business baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Dropbox for Business upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Dropbox for Business måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Dropbox for Business](#configure-dropbox-for-business-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Dropbox for Business-testanvändare](#create-dropbox-for-business-test-user)** – så att det finns en motsvarighet till Britta Simon i Dropbox for Business som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure Portal.

Gör följande för att konfigurera enkel inloggning med Azure AD med Dropbox for Business:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **Dropbox for Business**-programintegrering väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Dropbox for Business – enkel inloggning-information för domän och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du in en URL enligt följande mönster: `https://www.dropbox.com/sso/<id>`

    b. I textrutan **Identifierare (entitets-ID)** anger du ett värde: `Dropbox`

    > [!NOTE]
    > Föregående inloggnings-URL är inte verkligt. Du uppdaterar värdet med den faktiska inloggnings-URL:en, som förklaras senare i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Dropbox for Business** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-dropbox-for-business-single-sign-on"></a>Konfigurera enkel inloggning i Dropbox for Business

1. Du kan konfigurera enkel inloggning på **Dropbox for Business**-sidan genom att gå till din Dropbox for Business-klient och logga in.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/ic769509.png "Konfigurera enkel inloggning")

2. Klicka på **användarikonen** och välj fliken **Inställningar**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure1.png "Konfigurera enkel inloggning")

3. Klicka på **Administratörskonsol** i den vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure2.png "Konfigurera enkel inloggning")

4. I **Administratörskonsol** klickar du på **Inställningar** i det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure3.png "Konfigurera enkel inloggning")

5. Välj alternativet **Enkel inloggning** under avsnittet **Autentisering**.

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure4.png "Konfigurera enkel inloggning")

6. Gör följande i avsnittet **Enkel inloggning**:  

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure5.png "Konfigurera enkel inloggning")

    a. Välj **Required** (Obligatoriskt) som alternativ i listrutan för **enkel inloggning**.

    b. Klicka på **Add sign-in URL** (Lägg till inloggnings-URL), och i textrutan **Identity provider sign-in URL** (Inloggnings-URL för identitetsprovider) klistrar in du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen och väljer sedan **Done** (Klart).

    ![Konfigurera enkel inloggning](./media/dropboxforbusiness-tutorial/configure6.png "Konfigurera enkel inloggning")

    c. Klicka på **Ladda upp certifikat** och bläddra sedan till din **Base64-kodade certifikatfil** som du har laddat ned från Azure-portalen.

    d. Klicka på **Kopiera länk** och klistra in det kopierade värdet i textrutan **Inloggnings-URL** I avsnittet **Domän och URL:er för Dropbox for Business** i Azure-portalen.

    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure Portal med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure Portal och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Dropbox for Business.

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program** och sedan **Dropbox for Business**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Skriv och välj **Dropbox for Business** i programlistan.

    ![Dropbox for Business-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-dropbox-for-business-test-user"></a>Skapa Dropbox for Business-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i Dropbox for Business. Dropbox for Business stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i Dropbox for Business skapas en ny efter autentisering.

>[!Note]
>Kontakta [Dropbox for Business-kundsupporten](https://www.dropbox.com/business/contact) om du behöver skapa en användare manuellt

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Dropbox for Business-panelen i åtkomstpanelen bör du automatiskt loggas in på Dropbox for Business som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

