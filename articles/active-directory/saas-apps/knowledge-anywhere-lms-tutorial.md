---
title: 'Självstudier: Azure Active Directory-integrering med Knowledge Anywhere LMS | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/14/2019
ms.author: jeedes
ms.openlocfilehash: 0ac3522d4182ef7cbf35eb8505dede2aff233937
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56302522"
---
# <a name="tutorial-azure-active-directory-integration-with-knowledge-anywhere-lms"></a>Självstudier: Azure Active Directory-integrering med Knowledge Anywhere LMS

Lär dig att integrera Knowledge Anywhere LMS med Azure Active Directory (Azure AD) i den här självstudien.
Att integrera Knowledge Anywhere LMS med Azure Active Directory ger dig följande fördelar:

* Du kan styra vem som har åtkomst till Knowledge Anywhere LMS i Azure AD.
* Du kan låta dina användare loggas in automatiskt på Knowledge Anywhere LMS (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure Active Directory-integrering med Knowledge Anywhere LMS, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad Knowledge Anywhere LMS-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Knowledge Anywhere LMS har stöd för **SP- och IDP**-initierad enkel inloggning
* Knowledge Anywhere LMS stöder **just-in-time**-användaretablering

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Lägga till Knowledge Anywhere LMS från galleriet

När du konfigurerar integreringen av Knowledge Anywhere LMS i Azure Active Directory, måste du lägga till Knowledge Anywhere LMS från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till Knowledge Anywhere LMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Knowledge Anywhere LMS** och väljer **Knowledge Anywhere LMS**  från resultatpanelen. Klicka sedan på knappen**Lägg till** för att lägga till programmet.

     ![Knowledge Anywhere LMS i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Knowledge Anywhere LMS baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Knowledge Anywhere LMS upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med Knowledge Anywhere LMS, måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Knowledge Anywhere LMS-testanvändare](#create-knowledge-anywhere-lms-test-user)** – för att ha en motsvarighet till Britta Simon i Knowledge Anywhere LMS som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Knowledge Anywhere LMS:

1. I [Azure-portalen](https://portal.azure.com/) på sidan för **Knowledge Anywhere LMS**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Information om Knowledge Anywhere LMS-domän och URL:er med enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren och svars-URL:en. Detta beskrivs senare i självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om Knowledge Anywhere LMS-domän och URL:er med enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Knowledge Anywhere LMS-klientens supportteam](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Knowledge Anywhere LMS** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-knowledge-anywhere-lms-single-sign-on"></a>Konfigurera enkel inloggning med Knowledge Anywhere LMS

1. Öppna administrationsportalen för Knowledge Anywhere LMS i ett nytt webbläsarfönster.

2. Välj fliken **Webbplats**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure1.png)

3. Välj på fliken **SAML-inställningar**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure2.png)

4. Klicka på **Lägg till ny**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure3.png)

5. På sidan **Lägg till/uppdatera SAML-inställningar** utför du följande steg:

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Ange IDP-namnet för din organisation. För exempelvis: `Azure`.

    b. I textrutan **Entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifierare** som du kopierade från Azure-portalen.

    c. I textrutan **IDP-URL** klistrar du in **inloggnings-URL:en** som du kopierade från Azure-portalen.

    d. Öppna den nedladdade certifikatfilen från Azure-portalen i Anteckningar, kopiera innehållet för certifikatet och klistra in det i textrutan **Certifikat**.

    e. I textrutan **Utloggnings-URL** klistrar du in värdet för den **utloggnings-URL** som du kopierade från Azure-portalen.

    f. Välj **Huvudwebbplats** i listrutan för **Domän**.

    g. Kopiera värdet i **Entitets-ID för SP** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    h. Kopiera värdet för **SP-svar (ACS) URL** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

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

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till Knowledge Anywhere LMS.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Knowledge Anywhere LMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Knowledge Anywhere LMS**.

    ![Knowledge Anywhere LMS-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Skapa testanvändare för Knowledge Anywhere LMS

I det här avsnittet skapas en användare som heter Britta Simon i Knowledge Anywhere LMS. Knowledge Anywhere LMS stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Knowledge Anywhere LMS, skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Knowledge Anywhere LMS-ikonen i åtkomstpanelen bör du automatiskt loggas in på den Knowledge Anywhere LMS som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)