---
title: 'Självstudier: Azure Active Directory-integrering med iProva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iProva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5b66b3cf2ec038107293da7a821fcb75273fc9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718350"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Självstudier: Azure Active Directory-katalogintegrering med iProva

I den här självstudien lär du dig hur du integrerar iProva med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar iProva med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till iProva.
* Du kan göra så att dina användare automatiskt loggas in på iProva (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med iProva behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* iProva-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* iProva stöder **SP**-initierad enkel inloggning

## <a name="adding-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

För att kunna konfigurera integreringen av iProva i Azure AD måste du lägga till iProva från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till iProva från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **iProva** i sökrutan, välj **iProva** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

    ![iProva i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med iProva baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i iProva upprättas.

För att kunna konfigurera och testa enkel inloggning med Azure AD med iProva måste du slutföra följande byggblock:

1. **[Hämta konfigurationsinformation från iProva](#retrieve-configuration-information-from-iprova)**  som en förberedelse för nästa steg.
2. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
3. **[Konfigurera enkel inloggning för iProva](#configure-iprova-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
4. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Skapa iProva-testanvändare](#create-iprova-test-user)** – så att det finns en motsvarighet till Britta Simon i iProva som är länkad till Azure AD-representationen av användaren.
7. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

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

Gör följande för att konfigurera enkel inloggning med iProva:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **iProva** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![iProva – enkel inloggning-information för domän och -URL:er](common/sp-identifier-reply.png)

    a. Fyll i rutan **Identifierare** med det värde som visas bakom etiketten **EntityID** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    b. Fyll i rutan **Svars-URL** med det värde som visas bakom etiketten **Svars-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    c. Fyll i rutan **Inloggnings-URL** med det värde som visas bakom etiketten **Inloggnings-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

5. iProva-programmet förväntar sig att SAML-intyg har ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut| Namnrymd  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på kopieringsknappen i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** om du vill kopiera **URL:en för federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurera enkel inloggning i iProva

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

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till iProva.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **iProva**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **iProva** i programlistan.

    ![iProva-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-iprova-test-user"></a>Skapa iProva-testanvändare

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Användare** i panelen **Användare och användargrupper**.

5. Välj **Lägg till**.

6. I den **användarnamn** anger användarnamnet för användaren som `BrittaSimon@contoso.com`.

7. I den **fullständigt namn** , ange ett fullständigt namn för användaren som **BrittaSimon**.

8. Välj alternativet **Inget lösenord (använd enkel inloggning)**.

9. I den **e-postadress** anger du e-postadressen för användaren som `BrittaSimon@contoso.com`.

10. Rulla ned till slutet av sidan och välj **Slutför**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på iProva-panelen i åtkomstpanelen så bör du automatiskt loggas in på den iProva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)