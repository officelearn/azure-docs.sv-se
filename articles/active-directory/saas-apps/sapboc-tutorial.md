---
title: 'Självstudier: Azure Active Directory-integrering med SAP Business Object Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Business Object Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c64fb2dc4c494252f0078b8d23e05b15f07ebe00
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894571"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>Självstudier: Azure Active Directory-integrering med SAP Business Object Cloud

I den här självstudien får du lära dig hur du integrerar SAP Business Object Cloud med Azure Active Directory (Azure AD).
Genom att integrera SAP Business Object Cloud med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till SAP Business Object Cloud.
* Du kan göra så att dina användare automatiskt loggas in på SAP Business Object Cloud (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAP Business Object Cloud behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP Business Object Cloud-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Business Object Cloud stöder **IDP**-initierad enkel inloggning

## <a name="adding-sap-business-object-cloud-from-the-gallery"></a>Lägga till SAP Business Object Cloud från galleriet

För att konfigurera integreringen av SAP Business Object Cloud till Azure AD behöver du lägga till SAP Business Object Cloud från galleriet till listan över hanterade SaaS-appar.

**Lägg till SAP Business Object Cloud från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAP Business Object Cloud**, väljer **SAP Business Object Cloud** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![SAP Business Object Cloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med SAP Business Object Cloud baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Business Object Cloud upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med SAP Business Object Cloud behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP Business Object Cloud](#configure-sap-business-object-cloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SAP Business Object Cloud-testanvändare](#create-sap-business-object-cloud-test-user)** – för att ha en motsvarighet för Britta Simon i SAP Business Object Cloud som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med SAP Business Object Cloud:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **SAP Business Object Cloud**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SAP Business Object Cloud-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL enligt följande mönster:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Värdena i dessa URL:er är bara exempel. Uppdatera värden med de faktisk inloggnings-URL och identifierar-URL. Hämta inloggnings-URL genom att kontakta [supportteamet för SAP Business Object Cloud-klienten](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Du kan hämta identifierar-URL genom att ladda ned SAP Business Object Cloud-metadata från administratörskonsolen. Detta beskrivs senare i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-sap-business-object-cloud-single-sign-on"></a>Konfigurera enkel inloggning för SAP Business Object Cloud

1. I ett annat webbläsarfönster loggar du in på din SAP Business Object Cloud-företagsplats som administratör.

2. Välj **Meny** > **System** > **Administration**.
    
    ![Välj Meny, sedan System och därefter Administration](./media/sapboc-tutorial/config1.png)

3. På fliken **Säkerhet** väljer du ikonen **Redigera** (pennan).
    
    ![På fliken Säkerhet väljer du ikonen Redigera](./media/sapboc-tutorial/config2.png)  

4. För **Autentiseringsmetod** väljer du **SAML Single Sign-On (SSO)** (Enkel inloggning (SSO) med SAML).

    ![Välja enkel inloggning med SAML som autentiseringsmetod](./media/sapboc-tutorial/config3.png)  

5. Ladda ned metadata för tjänstleverantör (steg 1) genom att välja **Ladda ned**. I metadatafilen letar du upp och kopierar värdet för **entityID**. I dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen klistrar du in värdet i rutan **Identifierare**.

    ![Kopiera och klistra in värdet för entityID](./media/sapboc-tutorial/config4.png)  

6. Ladda upp metadata för tjänstleverantör (steg 2) i den fil som du laddade ned från Azure-portalen genom att gå till **Upload Identity Provider metadata** (Ladda upp metadata för tjänstleverantör) och välja **Ladda upp**.  

    ![Under Upload Identity Provider metadata (Ladda upp metadata för tjänstleverantör) väljer du Ladda upp](./media/sapboc-tutorial/config5.png)

7. I listan **Användarattribut** väljer du det användarattribut (steg 3) som du vill använda för implementeringen. Det här användarattributet mappar till identitetsprovidern. Du ange ett anpassat attribut på användarens sida genom att använda alternativet **Custom SAML Mapping** (Anpassad SAML-mappning). Eller så kan du välja antingen **Email** (E-post) eller **USER ID** (Användar-ID) som användarattribut. I vårt exempel valde vi **Email** (E-post) eftersom vi mappade anspråket för användar-ID med attributet **userprincipalname** i avsnittet **Användarattribut och anspråk** i Azure-portalen. Detta ger en unik användar-e-post, som skickas till SAP Business Object Cloud-programmet i varje lyckat SAML-svar.

    ![Välja användarattribut](./media/sapboc-tutorial/config6.png)

8. Verifiera kontot med identitetsprovidern (steg 4) genom att gå till rutan **Login Credential (Email)** (Autentiseringsuppgifter (e-post)) och ange användarens e-postadress. Välj sedan **Verify Account** (Verifiera konto). Systemet lägger till autentiseringsuppgifter till användarkontot.

    ![Ange e-postadress och välj Verify Account (Verifiera konto)](./media/sapboc-tutorial/config7.png)

9. Välj ikonen **Spara**.

    ![Ikonen Spara](./media/sapboc-tutorial/save.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till SAP Business Object Cloud.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **SAP Business Object Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SAP Business Object Cloud**.

    ![SAP Business Object Cloud-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-business-object-cloud-test-user"></a>Skapa SAP Business Object Cloud-testanvändare

Azure AD-användare måste etableras i SAP Business Object Cloud innan de kan logga in på SAP Business Object Cloud. När det gäller SAP Business Object Cloud är etablering en manuell aktivitet.

Så här etablerar du ett användarkonto:

1. Logga in på din SAP Business Object Cloud-företagsplats som administratör.

2. Välj **Meny** > **Säkerhet** > **Användare**.

    ![Lägga till medarbetare](./media/sapboc-tutorial/user1.png)

3. På sidan **Användare** lägger du till nya användaruppgifter genom att välja **+**. 

    ![Sidan Lägg till användare](./media/sapboc-tutorial/user4.png)

    Utför sedan följande steg:

    a. I rutan **USER ID** (Användar-ID) anger du användar-ID för användaren: **Britta**.

    b. I rutan **FIRST NAME** (Förnamn) anger du förnamn för användaren: **Britta**.

    c. I rutan **LAST NAME** (Efternamn) anger du efternamn för användaren: **Simon**.

    d. I rutan **DISPLAY NAME** (Visningsnamn) anger du fullständigt namn för användaren: **Britta Simon**.

    e. I den **e** anger e-postadressen för användaren som **brittasimon\@contoso.com**.

    f. På sidan **Select Roles** (Välj roller) väljer du rätt roll för användaren och väljer sedan **OK**.

      ![Välja en roll](./media/sapboc-tutorial/user3.png)

    g. Välj ikonen **Spara**.    

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAP Business Object Cloud-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Business Object Cloud som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

