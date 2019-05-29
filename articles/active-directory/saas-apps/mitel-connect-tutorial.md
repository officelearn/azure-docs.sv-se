---
title: 'Självstudier: Azure Active Directory-integrering med Mitel ansluta | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mitel ansluta.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 611867cf5606d5a1078706e1c0f67f673a7fa500
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254612"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Självstudier: Azure Active Directory-integrering med Mitel MiCloud ansluta

I de här självstudierna lär du dig att integrera Mitel MiCloud ansluta med Azure Active Directory (AD Azure). Integrera MiCloud ansluta med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till MiCloud ansluta appar med sina autentiseringsuppgifter för företaget.
* Du kan aktivera användare på ditt konto för att vara automatiskt inloggad MiCloud ansluta (Single Sign-On) med sina Azure AD-konton.


Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med MiCloud ansluta, behöver du följande objekt:

* En Azure AD-prenumeration

  Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Ett Mitel MiCloud ansluta konto

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa Azure AD enkel inloggning (SSO).

* Har stöd för Mitel ansluta **SP** -initierad SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Att lägga till Mitel ansluta från galleriet

Du måste lägga till Mitel ansluta från galleriet i din lista över hanterade SaaS-appar i Azure portal om du vill konfigurera integreringen av Mitel ansluta till Azure AD.

**Om du vill lägga till Mitel ansluta från galleriet, gör du följande:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Klicka på **företagsprogram** och klicka sedan på **alla program**.

    ![Bladet för Enterprise-program](common/enterprise-applications.png)

3. Klicka på **nytt program**.

    ![Knappen Nytt program](common/add-new-app.png)

4. Typ **Mitel ansluta** i sökfältet, klickar du på **Mitel ansluta** resultatrutan och klicka sedan på **Lägg till**.

     ![Mitel ansluta i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet får du konfigurera och testa Azure AD enkel inloggning med MiCloud ansluta baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i MiCloud ansluta upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med MiCloud Connect, måste du utföra följande steg:

1. **[Konfigurera MiCloud ansluta för enkel inloggning med Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**  – om du vill ge användarna att använda den här funktionen och konfigurera inställningar för enkel inloggning på programsidan.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
4. **[Skapa en testanvändare Mitel MiCloud ansluta](#create-a-mitel-micloud-connect-test-user)**  – du har en motsvarighet för Britta Simon på ditt MiCloud Connect-konto som är länkad till en Azure AD-representation av användaren.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurera MiCloud ansluta för enkel inloggning med Azure AD

I det här avsnittet ska du aktivera Azure AD enkel inloggning för MiCloud Anslut i Azure-portalen och konfigurera ditt MiCloud Connect-konto för att tillåta SSO med hjälp av Azure AD.

Om du vill konfigurera MiCloud ansluta med enkel inloggning för för Azure AD, är det enklast att öppna Azure portal och Mitel kontoportalen sida vid sida. Du behöver kopiera information från Azure-portalen till Mitel kontoportalen och vissa från Mitel-kontoportalen på Azure Portal.


1. Att öppna konfigurationssidan i den [Azure-portalen](https://portal.azure.com/), gör du följande:

    a. På den **Mitel ansluta** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

    b. I den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **SAML**.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)
    
    SAML-baserad inloggning på sidan visas.

2. Öppna konfigurationsdialogrutan i Mitel-kontoportalen genom att göra följande:

    a. På den **Phone System** -menyn klickar du på **tilläggsfunktioner**.

    b. Till höger om **enkel inloggning**, klickar du på **aktivera** eller **inställningar**.
    
    Dialogrutan Anslut enkel inloggning inställningar visas.
    
3. Välj den **aktivera enkel inloggning** markerar du kryssrutan.
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. I Azure-portalen klickar du på den **redigera** ikon i den **SAML grundkonfiguration** avsnittet.
    ![image](common/edit-urls.png)

    Dialogrutan grundläggande SAML-konfiguration visas.

5.  Kopiera Webbadressen från den **Mitel identifierare (entitets-ID)** fältet Mitel-kontoportalen och klistra in den i den **identifierare (entitets-ID)** i Azure-portalen.

6. Kopiera Webbadressen från den **svars-URL (försäkran URL för Konsumenttjänst)** fältet Mitel-kontoportalen och klistra in den i den **svars-URL (försäkran URL för Konsumenttjänst)** i Azure-portalen.  
   ![image](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. I den **inloggnings-URL** textruta, skriver du något av följande webbadresser:

    * **https://portal.shoretelsky.com** – att använda Mitel kontoportalen som standardprogram för Mitel
    * **https://teamwork.shoretel.com** – att använda samarbete som standardprogram för Mitel

    **Obs!** Standard Mitel programmet är program som nås när en användare klickar på panelen Mitel ansluta i åtkomstpanelen. Det här är program som nås när du gör en test-installation från Azure AD.

8. Klicka på **spara** i den **SAML grundkonfiguration** dialogrutan i Azure-portalen.

9. I den **SAML-signeringscertifikat** avsnittet på den **SAML-baserad inloggning** i Azure portal, klickar du på **hämta** bredvid **certifikat (Base64)** att ladda ned den **signeringscertifikat** och spara det på din dator.
    ![image](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Öppna filen signeringscertifikat i en textredigerare, kopiera alla data i filen och klistra sedan in data i den **signeringscertifikat** i Mitel kontoportalen. 
    ![image](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. I den **det Mitel ansluta** avsnittet på den **SAML-baserad inloggning** sida av Azure-portalen gör du följande:

    a. Kopiera Webbadressen från den **inloggnings-URL** fältet och klistrar in den i den **logga in URL: en** i Mitel kontoportalen.

    b. Kopiera Webbadressen från den **Azure AD-identifierare** fältet och klistrar in den i den **entitets-ID** i Mitel kontoportalen.
    ![image](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klicka på **spara** på den **ansluta inloggnings-inställningar för enkel** dialogrutan Mitel-kontoportalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en användare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen, i den vänstra rutan klickar du på **Azure Active Directory**, klickar du på **användare**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](common/users.png)

2. Klicka på **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör följande i egenskapsdialogrutan för användaren:

    ![Dialogrutan användare](common/user-properties.png)

    a. I den **namn** skriver **BrittaSimon**.
  
    b. I den **användarnamn** skriver brittasimon @\<företagsdomänen\>.\< tillägget\>.  
Till exempel BrittaSimon@contoso.com.

    c. Välj den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Mitel ansluta.

1. I Azure-portalen klickar du på **företagsprogram**, och klicka sedan på **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program klickar du på **Mitel ansluta**.

    ![Länken Mitel ansluta i listan med program](common/all-applications.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**, klicka sedan på **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** klicka sedan på **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran, väljer du rätt roll för användaren i listan i den **Välj roll** dialogrutan och klicka sedan på **Välj** längst ned på skärmen.

7. I den **Lägg till tilldelning** dialogrutan klickar du på **tilldela**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Skapa en Mitel MiCloud ansluta testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon på ditt MiCloud Connect-konto. Användare måste skapas och aktiveras innan du använder enkel inloggning.

Mer information om att lägga till användare i Mitel-kontoportalen, finns i den [att lägga till en användare](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) artikel i Mitel Knowledge Base.

Skapa en användare på ditt MiCloud Connect-konto med följande uppgifter:

  * **Namn:** Britta Simon

* **Företagets e-postadress:** `brittasimon@<yourcompanydomain>.<extension>`   
(Exempel: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com))

* **Användarnamn:** `brittasimon@<yourcompanydomain>.<extension> `  
(Exempel: [ brittasimon@contoso.com ](mailto:brittasimon@contoso.com); användarens användarnamn är vanligtvis samma som användarens företags e-postadress)

**Obs!** Användarens MiCloud ansluta användarnamnet måste vara identiska med användarens e-postadress i Azure.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Mitel ansluta i åtkomstpanelen du ska omdirigeras automatiskt för att logga in till programmet MiCloud ansluter du har konfigurerat som standard i den **inloggnings-URL** fält. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
