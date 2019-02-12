---
title: 'Självstudier: Azure Active Directory-integrering med iProva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: e07f893296ec20e8c722fc977f93a38a797fe5c9
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55490064"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Självstudier: Azure Active Directory-katalogintegrering med iProva

I den här självstudien lär du dig hur du integrerar iProva med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar iProva med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till iProva.
* Du kan göra så att dina användare automatiskt loggas in på iProva (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om SaaS-appintegrering (Programvara som en tjänst) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med iProva behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion på webbplatsen för [Microsoft Azure](https://azure.microsoft.com/pricing/free-trial/).
* En iProva-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö:

* iProva stöder SP-initierad enkel inloggning.

## <a name="add-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

För att konfigurera integreringen av iProva i Azure AD måste du lägga till iProva från galleriet till din lista över hanterade SaaS-appar.

Följ dessa steg för att lägga till iProva från galleriet:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **iProva**. Välj **iProva** i resultatpanelen och välj sedan **Lägg till** för att lägga till programmet.

     ![iProva i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med iProva baserat på en testanvändare med namnet Britta Simon.
För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i iProva.

För att kunna konfigurera och testa enkel inloggning i Azure AD med iProva slutför du följande byggstenar:

- [Hämta konfigurationsinformation från iProva](#retrieve-configuration-information-from-iprova) som förberedelse för kommande steg.
- [Konfigurera enkel inloggning i Azure AD](#configure-azure-ad-single-sign-on) så att användarna kan använda den här funktionen.
- [Konfigurera enkel inloggning för iProva](#configure-iprova-single-sign-on) för att konfigurera inställningarna för enkel inloggning på programsidan.
- [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
- [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.
- [Skapa en iProva-testanvändare](#create-an-iprova-test-user) så att det finns en motsvarighet till Britta Simon i iProva som är länkad till Azure AD-representationen av användaren.
- [Testa enkel inloggning](#test-single-sign-on) för att verifiera huruvida konfigurationen fungerar.

### <a name="retrieve-configuration-information-from-iprova"></a>Hämta konfigurationsinformation från iProva

I det här avsnittet hämtar du information från iProva för att konfigurera enkel inloggning i Azure AD.

1. Öppna en webbläsare och gå till sidan **SAML2-info** i iProva med hjälp av följande URL-mönster:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

     ![Visa SAML2-informationssidan i iProva](media/iprova-tutorial/iprova-saml2-info.png)

2. Låt webbläsarfliken vara öppen medan du fortsätter med nästa steg i en annan webbläsarflik.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med iProva.

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **iProva** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Ikonen Redigera i Grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** följer du dessa steg.

    a. Fyll i rutan **Identifierare** med det värde som visas bakom etiketten **EntityID** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    b. Fyll i rutan **Svars-URL** med det värde som visas bakom etiketten **Svars-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    c. Fyll i rutan **Inloggnings-URL** med det värde som visas bakom etiketten **Inloggnings-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    ![Information om enkel inloggning med iProva-domän och -URL:er](common/sp-identifier-reply.png)

5. iProva-programmet förväntar sig SAML-försäkran i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Dialogrutan Användarattribut](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i föregående bild. Följ de här stegen.

    | Namn | Källattribut| Namnområde |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|
    | | |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Användaranspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    b. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    c. I listan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj alternativet **Källa** som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    g. Välj **Spara**.

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), väljer ikonen **Kopiera** för att kopiera **App Federation Metadata-URL** och sparar den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurera enkel inloggning för iProva

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Allmänt** i panelen **Systeminställningar**.

5. Välj **Redigera**.

6. Rulla ned till **Åtkomstkontroll**.

    ![Inställningar för åtkomstkontroll i iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Hitta inställningen **Users are automatically logged on with their network accounts** (Användare loggas automatiskt in med nätverkskonton) och ändra det till **Yes, authentication via SAML** (Ja, autentisering visa SAML). Ytterligare alternativ visas nu.

8. Välj **Konfigurera**.

9. Välj **Nästa**.

10. iProva frågar dig om du vill ladda ned federationsdata från en URL eller ladda upp dem från en fil. Välj alternativet **Från URL**.

    ![Ladda ned Azure AD-metadata](media/iprova-tutorial/iprova-download-metadata.png)

11. Klistra in den metadata-URL som du sparade i det sista steget i kapitlet Konfigurera enkel inloggning med Azure AD.

12. Välj den pilformade knappen för att ladda ned metadata från Azure AD.

13. När nedladdningen är slutförd visas bekräftelsemeddelandet om **Valid Federation Data file downloaded** (Giltig federationsdatafil har laddats ned).

14. Välj **Nästa**.

15. Hoppa över alternativet **Testa inloggning** för tillfället och välj **Nästa**.

16. I listrutan **Claim to use** (Anspråk att använda) väljer du **windowsaccountname**.

17. Välj **Slutför**.

18. Nu kan du gå tillbaka till sidan **Redigera allmänna inställningar**. Rulla ned till slutet av sidan och välj **OK** för att spara konfigurationen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i Azure-portalen med namnet Britta Simon.

1. I Azure-portalen går du till den vänstra panelen och väljer **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Länkarna Användare och grupper samt Alla användare](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialogrutan **Användare** följer du dessa steg.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du ett namn såsom **BrittaSimon**.
  
    b. I rutan **Användarnamn** anger du *yourname@yourcompanydomain.extension*. 
    Ett exempel är BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.

    d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till iProva.

1. I Azure-portalen väljer du **Företagsprogram** > **Alla program** > **iProva**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **iProva**.

    ![Länken för iProva i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken Användare och grupper](common/users-groups-blade.png)

4. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Dialogrutan Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan **Användare** och väljer **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-an-iprova-test-user"></a>Skapa en iProva-testanvändare

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Användare** i panelen **Användare och användargrupper**.

5. Välj **Lägg till**.

6. I rutan **Användarnamn** anger du *brittasimon@yourcompanydomain.extension*. 
    Ett exempel är BrittaSimon@contoso.com.

7. I rutan **Fullständigt namn** anger du ett fullständigt namn såsom **BrittaSimon**.

8. Välj alternativet **Inget lösenord (använd enkel inloggning)**.

9. I rutan **E-postadress** anger du *yourname@yourcompanydomain.extension*. 
   Ett exempel är BrittaSimon@contoso.com.

10. Rulla ned till slutet av sidan och välj **Slutför**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du klickar på iProva-panelen i åtkomstpanelen bör du automatiskt loggas in på iProva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [iProva – Konfigurera enkel inloggning med SAML2](https://webshare.iprova.nl/0wqwm45yn09f5poh/Document.aspx)
