---
title: 'Självstudier: Azure Active Directory-integrering med iPass SmartConnect | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bf8c7e76aa6dd69cc505be237e47d8e10e7e8db5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59264870"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Självstudier: Azure Active Directory-integrering med iPass SmartConnect

I den här självstudien får du lära dig hur du integrerar iPass SmartConnect med Azure Active Directory (AD Azure).
Integrera iPass SmartConnect med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till iPass SmartConnect.
* Du kan aktivera användarna att vara automatiskt inloggad till iPass SmartConnect (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iPass SmartConnect, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* iPass SmartConnect enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* iPass SmartConnect stöder **SP och IDP** -initierad SSO
* iPass SmartConnect stöder **Just In Time** etableringen av användare

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Att lägga till iPass SmartConnect från galleriet

För att konfigurera integrering av iPass SmartConnect i Azure AD, som du behöver lägga till iPass SmartConnect från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iPass SmartConnect från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **iPass SmartConnect**väljer **iPass SmartConnect** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![iPass SmartConnect i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning att fungera, en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iPass SmartConnect måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera iPass SmartConnect Single Sign-On](#configure-ipass-smartconnect-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa iPass SmartConnect testanvändare](#create-ipass-smartconnect-test-user)**  – du har en motsvarighet för Britta Simon i iPass SmartConnect som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med iPass SmartConnect:

1. I den [Azure-portalen](https://portal.azure.com/)på den **iPass SmartConnect** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![iPass SmartConnect domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![iPass SmartConnect domän och URL: er med enkel inloggning för information](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. iPass SmartConnect program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn |  Källattribut|
    | ---------------| ----------|
    | firstName | user.givenname |
    | lastName | user.surname |
    | e-post | user.userprincipalname |
    | användarnamn | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. På den **konfigurera iPass SmartConnect** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ipass-smartconnect-single-sign-on"></a>Konfigurera iPass SmartConnect enkel inloggning

Att konfigurera enkel inloggning på **iPass SmartConnect** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [iPass SmartConnect supportteam](mailto:help@ipass.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iPass SmartConnect.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **iPass SmartConnect**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **iPass SmartConnect**.

    ![IPass SmartConnect länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ipass-smartconnect-test-user"></a>Skapa iPass SmartConnect testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i iPass SmartConnect. Arbeta med [iPass SmartConnect supportteam](mailto:help@ipass.com) att lägga till användare eller domän som behövs för att bli godkänd i iPass SmartConnect-plattformen. Om domänen har lagts till av teamet, kommer användare får automatiskt tillhandahållas för iPass SmartConnect-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

**Om du vill testa programmet i SP-initierat flödet utför du följande steg:**

a. Ladda ned windows iPass SmartConnect klienten [här](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installera klienten och starta.

c. Klicka på **börjar**.

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Ange användarnamnet med domän. Klicka på **fortsätta**. Detta kommer att omdirigeras till inloggningssidan för Azure

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Aktivering av klienten kommer att startas efter en lyckad autentisering. Klienten ska aktiveras.

**Om du vill testa programmet i IdP-initierad flödet utför du följande steg:**

a. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klicka på iPass SmartConnect app.

c. Startar SSA sidan, klickar du på **ladda ned appen för Windows** installera iPass SmartConnect klienten.

![IPass SmartConnect länk i listan med program](./media/ipasssmartconnect-tutorial/testing4.png)

d. När installationen av klienten vid första starten kommer automatiskt startar aktivering när du godkänner villkoren.

e. Om aktiveringen inte startar, klicka på knappen Aktivera på SSA sidan för att påbörja aktiveringen.

f. Klienten ska aktiveras.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)