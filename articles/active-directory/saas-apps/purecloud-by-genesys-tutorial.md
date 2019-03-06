---
title: 'Självstudier: Azure Active Directory-integrering med PureCloud by Genesys | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: c9b2770f861098993623d69f6b9f6a1577c9cf27
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56890615"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Självstudier: Azure Active Directory-integrering med PureCloud by Genesys

I den här självstudien lär du dig att integrera PureCloud by Genesys med Azure Active Directory (Azure AD).
När du integrerar PureCloud by Genesys med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till PureCloud by Genesys från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på PureCloud by Genesys (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med PureCloud by Genesys behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* PureCloud by Genesys-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* PureCloud by Genesys har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>Lägga till PureCloud by Genesys från galleriet

För att konfigurera integreringen av PureCloud by Genesys i Azure AD måste du lägga till PureCloud by Genesys från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till PureCloud by Genesys från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **PureCloud by Genesys**, väljer **PureCloud by Genesys** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![PureCloud by Genesys i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med PureCloud by Genesys baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i PureCloud by Genesys upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med PureCloud by Genesys måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för PureCloud by Genesys](#configure-purecloud-by-genesys-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa PureCloud by Genesys-testanvändare](#create-purecloud-by-genesys-test-user)** – för att ha en motsvarighet för Britta Simon i PureCloud by Genesys som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med PureCloud by Genesys:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **PureCloud by Genesys** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Information om enkel inloggning för PureCloud by Genesys-domän och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL enligt din region:
    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. I textrutan **Svars-URL** skriver du en URL enligt din region:
    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning för PureCloud by Genesys-domän och -URL:er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du en URL enligt din region:
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. PureCloud by Genesys-programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

7. Utöver ovanstående förväntar sig PureCloud by Genesys-programmet att några fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | E-post | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

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

9. I avsnittet **Konfigurera PureCloud by Genesys** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>Konfigurera enkel inloggning med PureCloud by Genesys

1. Öppna ett nytt webbläsarfönster och logga in i PureCloud by Genesys som administratör.

2. Klicka på **Administratör** längst upp och gå till **Enkel inloggning** under **Integrationer**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Växla till fliken **ADFS/Azure AD(Premium)** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Klicka på **Bläddra** för att ladda upp det base-64-kodade certifikat som du har laddat ned från Azure-portalen till **ADFS-certifikatet**.

    b. I textrutan för **URI för ADFS-utfärdare** klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I textrutan **Mål-URI** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    d. För värdet **Identifierare för förlitande part** behöver du gå till Azure-portalen, programintegreringssidan för **PureCloud by Genesys**, klicka på fliken **Egenskaper** och kopiera värdet **Program-ID**. Klistra in det i textrutan **Identifierare för förlitande part**. 

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Klicka på **Spara**   

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till PureCloud by Genesys.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **PureCloud by Genesys**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **PureCloud by Genesys**.

    ![PureCloud by Genesys-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-purecloud-by-genesys-test-user"></a>Skapa PureCloud by Genesys-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in i PureCloud by Genesys måste de etableras i PureCloud by Genesys. I PureCloud by Genesys är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in i PureCloud by Genesys som administratör.

2. Klicka på **Administratör** längst upp och gå till **Personer** under **Personer och behörigheter**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure03.png)

3. På sidan Personer klickar du på **Lägg till person**.

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure04.png)

4. I popup-fönstret **Lägg till personer i organisationen** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. I textrutan **Fullständigt namn** anger du namnet på användaren, t.ex. **Brittasimon**.

    b. I textrutan **E-post** anger du användarens e-postadress som **brittasimon@contoso.com**.
    
    c. Klicka på **Skapa**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på PureCloud by Genesys-panelen i åtkomstpanelen bör du automatiskt loggas in på PureCloud by Genesys som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

