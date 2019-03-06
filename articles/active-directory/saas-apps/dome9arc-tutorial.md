---
title: 'Självstudier: Azure Active Directory-integrering med Dome9 Arc | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dome9 Arc.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4c12875f-de71-40cb-b9ac-216a805334e5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: da98268d0a6ee015c848e9fe91cb3deecf28c4f4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875709"
---
# <a name="tutorial-azure-active-directory-integration-with-dome9-arc"></a>Självstudier: Azure Active Directory-integrering med Dome9 Arc

I den här självstudien lär du dig att integrera Dome9 Arc med Azure Active Directory (AD Azure).
När du integrerar Dome9 Arc med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till Dome9 Arc från Azure AD.
* Du kan låta dina användare automatiskt loggas in på Dome9 Arc (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Dome9 Arc behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Dome9 Arc-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Dome9 Arc stöder **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-dome9-arc-from-the-gallery"></a>Lägga till Dome9 Arc från galleriet

För att konfigurera integrering av Dome9 Arc i Azure AD behöver du lägga till Dome9 Arc från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Dome9 Arc från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Dome9 Arc** i sökrutan, välj **Dome9 Arc** i resultatpanelen och klicka på knappen **Lägg till** för att lägga till programmet.

     ![Dome9 Arc i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet får du konfigurera och testa enkel inloggning i Azure AD med Dome9 Arc baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Dome9 Arc upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Dome9 Arc behöver du göra följande:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Dome9 Arc](#configure-dome9-arc-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Dome9 Arc-testanvändare](#create-dome9-arc-test-user)** – för att ha en motsvarighet för Britta Simon i Dome9 Arc som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Dome9 Arc:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Dome9 Arc** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Dome9 Arc-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://secure.dome9.com/`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/yourcompanyname`

    > [!NOTE]
    > Du väljer värdet för ditt företagsnamn i dome9-administratörsportalen, vilket förklaras senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Dome9 Arc-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://secure.dome9.com/sso/saml/<yourcompanyname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [Dome9 Arc-supportteamet](mailto:support@dome9.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Dome9 Arc-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn |  Källattribut|
    | ---------------| --------------- |
    | memberof | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Dome9 Arc** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-dome9-arc-single-sign-on"></a>Konfigurera enkel inloggning med Dome9 Arc

1. Logga in på Dome9 Arc-företagswebbplatsen som administratör i ett annat webbläsarfönster.

2. Klicka på **profilinställningarna** uppe i högra hörnet och klicka sedan på **Account Settings** (Kontoinställningar). 

    ![Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure1.png)

3. Gå till **SSO** och klicka sedan på **ENABLE**.

    ![Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure2.png)

4. Utför följande steg i konfigurationsavsnittet för enkel inloggning:

    ![Dome9 Arc-konfiguration](./media/dome9arc-tutorial/configure3.png)

    a. Ange företagets namn i textrutan **Account ID** (Konto-ID). Det här värdet används i svars-URL:en som nämndes tidigare i avsnittet om URL för Azure-portalen.

    b. I textrutan **Issuer**  (Utfärdare) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I textrutan **Idp endpoint url** (url till Idp-slutpunkt) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).

    e. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Dome9 Arc.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Dome9 Arc**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Dome9 Arc**.

    ![Dome9 Arc-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-dome9-arc-test-user"></a>Skapa Dome9 Arc-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på Dome9 Arc måste de etableras i programmet. Dome9 Arc stöder just-in-time-etablering, men för att det ska fungera korrekt måste användaren välja en viss **roll** och tilldela den till användaren.

   >[!Note]
   >Kontakta [Dome9 Arc-supportteamet](https://dome9.com/about/contact-us/) för att få hjälp med att skapa **rollen** och få mer information.

**Utför följande steg om du vill etablera ett användarkonto manuellt:**

1. Logga in som administratör på din Dome9 Arc-företagswebbplats.

2. Klicka på **Users & Roles** (Användare och roller) och sedan på **Users** (Användare).

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user1.png)

3. Klicka på **LÄGG TILL ANVÄNDARE**.

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user2.png)

4. I avsnittet **Skapa användare** utför du följande steg:

    ![Lägga till medarbetare](./media/dome9arc-tutorial/user3.png)

    a. I textrutan **E-post** skriver du e-postadressen för användaren, till exempel Brittasimon@contoso.com.

    b. I textrutan för **förnamn** skriver du förnamnet på användaren, till exempel Britta.

    c. I textrutan för **efternamn** skriver du efternamnet, till exempel Simon.

    d. Välj **På** för **användaren**.

    e. Klicka på **SKAPA**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Dome9 Arc-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Dome9 Arc som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

