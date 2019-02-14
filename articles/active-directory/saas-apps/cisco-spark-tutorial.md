---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Spark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87161c79bc58387d97863581675bb49f1e09160f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56191562"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Självstudier: Azure Active Directory-integrering med Cisco Spark

Lär dig hur du integrerar Cisco Spark med Azure Active Directory (AD Azure) i den här självstudien.
När du integrerar Cisco Spark med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Cisco Spark.
* Du kan göra så att dina användare automatiskt loggas in på Cisco Spark (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Cisco Spark behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Spark-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cisco Spark har stöd för **SP**-initierad enkel inloggning

## <a name="adding-cisco-spark-from-the-gallery"></a>Lägga till Cisco Spark från galleriet

För att konfigurera integrering av Cisco Spark i Azure AD måste du lägga till Cisco Spark från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco Spark från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cisco Spark**, väljer **Cisco Spark** på resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Cisco Spark i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Cisco Spark baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cisco Spark upprättas.

Konfigurera och testa enkel inloggning med Azure AD med Cisco Spark genom att slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cisco Spark](#configure-cisco-spark-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Cisco Spark-testanvändare](#create-cisco-spark-test-user)** – för att ha en motsvarighet till Britta Simon i Cisco Spark som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med Cisco Spark:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Cisco Spark**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Cisco Spark-domänen och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL: `https://web.ciscospark.com/#/signin`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://idbroker.webex.com/<companyname>`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [Cisco Spark-supporten](https://support.ciscospark.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Cisco Spark-programmet förväntar ett visst format för SAML-försäkran. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:
    
    | Namn |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **om att konfigurera Cisco Spark** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cisco-spark-single-sign-on"></a>Konfigurera enkel inloggning för Cisco Spark

1. Logga in i [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Välj **Settings** (Inställningar) och klicka på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Välj **Integrate a 3rd-party identity provider. (Avancerat)** (Integrera en tredjeparts identitetsprovider. (Avancerat)) och gå till nästa skärm.

4. På sidan **Import Idp Metadata** (Importera ldp-metadata) kan du antingen dra och släppa Azure AD-metadatafilen på sidan eller använda filbläddraren för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

6. Gå tillbaka till webbläsarfliken **Cisco Cloud Collaboration Management** (Ciscos samarbetshantering i molnet). Om testet lyckades väljer du **This test was successful. Enable Single Sign-On option** (Testet lyckades. Aktivera alternativet för enkel inloggning) och klickar på **Next** (Nästa).

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att bevilja åtkomst till Cisco Spark.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cisco Spark**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cisco Spark** i listan över program.

    ![Cisco Spark-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cisco-spark-test-user"></a>Skapa en testanvändare i Cisco Spark

I det här avsnittet skapar du en användare med namnet Britta Simon i Cisco Spark. I det här avsnittet skapar du en användare med namnet Britta Simon i Cisco Spark.

1. Logga in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. I rutan **Manage Users** (Hantera användare) väljer du **Manually add or modify users** (Ändra eller lägga till användare manuellt) och klickar på **Next** (Nästa).

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. I textrutan för **förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress: **britta.simon@contoso.com**.

5. Klicka på plustecknet för att lägga till Britta Simon. Klicka sedan på **Nästa**.

6. I rutan **Add Services for Users** (Lägg till tjänster för användare) klickar du på **Save** (Spara) och sedan på **Finish** (Slutför).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Cisco Spark-panelen i åtkomstpanelen bör du automatiskt loggas in på Cisco Spark som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
