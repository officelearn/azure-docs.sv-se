---
title: 'Självstudie: Azure Active Directory integrering med spets anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och avgränsnings anslutning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 204f540b-09f1-452b-a52f-78143710ef76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/03/2019
ms.author: jeedes
ms.openlocfilehash: 30a4dcbd15d8a47d99b3a61879a7e61938d3b133
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160533"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect"></a>Självstudie: Azure Active Directory integrering med MiCloud-anslutning

I den här självstudien får du lära dig att integrera MiCloud anslutning med Azure Active Directory (Azure AD). Att integrera MiCloud Connect med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till MiCloud Connect-appar med sina företags uppgifter.
* Du kan låta användare på ditt konto loggas in automatiskt till MiCloud Connect (enkel inloggning) med sina Azure AD-konton.


Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med MiCloud Connect behöver du följande objekt:

* En Azure AD-prenumeration

  Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Ett MiCloud Connect-konto

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien får du konfigurera och testa enkel inloggning (SSO) för Azure AD.

* Spetsig anslutning stöder **SP** -INITIERAd SSO

## <a name="adding-mitel-connect-from-the-gallery"></a>Lägga till spetsig anslutning från galleriet

Om du vill konfigurera integrering av tusentals anslutning till Azure AD måste du lägga till ett spets koppling från galleriet till listan över hanterade SaaS-appar i Azure Portal.

**Gör så här om du vill lägga till en spetsig anslutning från galleriet:**

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på **[Azure Portal](https://portal.azure.com)** .

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Klicka på **företags program** och klicka sedan på **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Klicka på **nytt program**.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv "på sidan **Anslut** i Sök fältet, klicka på **spets koppling** från resultat panelen och klicka sedan på **Lägg till**.

     ![Spets koppling i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning med Azure AD med MiCloud Connect baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i MiCloud Connect upprättas.

Du måste utföra följande steg för att konfigurera och testa enkel inloggning med MiCloud Connect för Azure AD:

1. **[Konfigurera MiCloud Connect för enkel inloggning med Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)** – så att användarna kan använda den här funktionen och konfigurera SSO-inställningar på program sidan.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
3. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
4. **[Skapa en MiCloud Connect-test](#create-a-mitel-micloud-connect-test-user)** för att få en motsvarighet till Britta Simon på ditt MiCloud Connect-konto som är länkat till användarens Azure AD-representation.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurera MiCloud Connect för enkel inloggning med Azure AD

I det här avsnittet ska du aktivera enkel inloggning med Azure AD för MiCloud Connect i Azure Portal och konfigurera ditt MiCloud Connect-konto för att tillåta enkel inloggning med Azure AD.

Om du vill konfigurera MiCloud ansluta med SSO för Azure AD är det enklast att öppna Azure Portal och säga konto portalen sida vid sida. Du måste kopiera lite information från Azure Portal till den säga konto portalen och vissa från den säga konto portalen till Azure Portal.


1. Om du vill öppna konfigurations sidan i [Azure Portal](https://portal.azure.com/)gör du följande:

    a. På sidan för att **ansluta** program integrering klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

    b. Klicka på **SAML**i dialog rutan **Välj metod för enkel inloggning** .

    ![Välja läge för enkel inloggning](common/select-saml-option.png)
    
    Sidan SAML-baserad inloggning visas.

2. Så här öppnar du dialog rutan konfiguration i den säga konto portalen:

    a. På **telefon system** -menyn klickar du på **Lägg till funktioner**.

    b. Till höger om **enkel inloggning**klickar du på **Aktivera** eller **Inställningar**.
    
    Dialog rutan Anslut inställningar för enkel inloggning visas.
    
3. Markera kryss rutan **aktivera enkel inloggning** .
    ![avbildning](./media/mitel-connect-tutorial/Mitel_Connect_Enable.png)


4. Klicka på ikonen **Redigera** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.
    ![avbildning](common/edit-urls.png)

    Dialog rutan grundläggande SAML-konfiguration visas.

5.  Kopiera URL: en från området **avgränsnings identifierare (enhets-ID)** i den säga konto portalen och klistra in den i fältet **identifierare (enhets-id)** i Azure Portal.

6. Kopiera URL: en från fältet **svars-URL (intyg om mottagar tjänst)** i den säga konto portalen och klistra in den i fältet **svars-URL (intyg om mottagar tjänst-url)** i Azure Portal.  
   ![avbildning](./media/mitel-connect-tutorial/Mitel_Azure_BasicConfig.png)

7. I text rutan **inloggnings-URL** skriver du en av följande URL: er:

    * **https://portal.shoretelsky.com** – om du vill använda en spetsig konto portal som standard program
    * **https://teamwork.shoretel.com** – om du vill använda lag lag som standard program

    **Obs! standard märkningen**är programmet som används när en användare klickar på panelen för att ansluta på åtkomst panelen. Detta är också det program som nås när du gör en test installation från Azure AD.

8. Klicka på **Spara** i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

9. I avsnittet **SAML-signeringscertifikat** på sidan **SAML-baserad inloggning** i Azure Portal klickar du på **Hämta** bredvid **certifikat (base64)** för att ladda ned **signerings certifikatet** och spara det på din dator.
    ![avbildning](./media/mitel-connect-tutorial/Azure_SigningCert.png)

10. Öppna signerings certifikat filen i en text redigerare, kopiera alla data i filen och klistra sedan in data i fältet **signerings certifikat** på konto portalen. 
    ![avbildning](./media/mitel-connect-tutorial/Mitel_Connect_SigningCert.png)

11. I avsnittet **installations avgränsning** på sidan **SAML-baserad inloggning** i Azure Portal gör du följande:

    a. Kopiera URL: en från fältet **inloggnings-URL** och klistra in den i fältet **inloggnings-URL** i den högra konto portalen.

    b. Kopiera URL: en från fältet för **Azure AD-identifieraren** och klistra in den i fältet **entitets-ID** på mål portalen.
    ![avbildning](./media/mitel-connect-tutorial/Mitel_Azure_SetupConnect.png)

12. Klicka på **Spara** i dialog rutan **Anslut inställningar för enkel inloggning** på den högra konto portalen.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare med namnet Britta Simon i Azure Portal.

1. Klicka på **Azure Active Directory**i Azure Portal i den vänstra rutan, klicka på **användare**och klicka sedan på **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Klicka på **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. I dialog rutan användar egenskaper utför du följande steg:

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** skriver du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du brittasimon @\<yourcompanydomain\>.\<-tillägg\>.  
Till exempel BrittaSimon@contoso.com.

    c. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till spets anslutning.

1. Klicka på **företags program**i Azure Portal och klicka sedan på **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program klickar du på **spets anslutning**.

    ![Länken för att ansluta i program listan](common/all-applications.png)

3. I menyn till vänster klickar du på **användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på **Lägg till användare**och sedan på **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialog rutan **användare och grupper** väljer du **Britta Simon** i listan **användare** och klickar sedan på **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på **Välj** längst ned på skärmen.

7. I dialog rutan **Lägg till tilldelning** klickar du på **tilldela**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Skapa en spetsig MiCloud Connect test User

I det här avsnittet skapar du en användare med namnet Britta Simon på ditt MiCloud Connect-konto. Användare måste skapas och aktive ras innan enkel inloggning används.

Mer information om hur du lägger till användare på den säga konto portalen finns i artikeln [lägga till en användare](https://docs.shoretel.com/connectcloud/Account/Users/AddUser) i den spetsa kunskaps basen.

Skapa en användare på ditt MiCloud Connect-konto med följande information:

  * **Namn:** Britta Simon

* **Företagets e-post adress:** `brittasimon@<yourcompanydomain>.<extension>`   
(Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))

* **Användar namn:** `brittasimon@<yourcompanydomain>.<extension>`  
(Exempel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). användarens användar namn är vanligt vis samma som användarens e-postadress till företaget)

**Obs:** Användarens MiCloud Connect-användarnamn måste vara identiskt med användarens e-postadress i Azure.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska du testa konfigurationen för enkel inloggning med Azure AD via åtkomst panelen.

När du klickar på panelen för att ansluta på åtkomst panelen bör du omdirigeras automatiskt för att logga in på MiCloud Connect-programmet som du konfigurerade som standard i fältet för **inloggnings-URL** . I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
