---
title: 'Självstudier: Azure Active Directory-integrering med Snowflake | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Snowflake.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3488ac27-0417-4ad9-b9a3-08325fe8ea0d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 6e1f76548e2433f9bc4b0b26b0894a92b7ec0aa0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092567"
---
# <a name="tutorial-azure-active-directory-integration-with-snowflake"></a>Självstudier: Azure Active Directory-integrering med Snowflake

I den här självstudien får du lära dig hur du integrerar Snowflake med Azure Active Directory (AD Azure).

Integrera Snowflake med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Snowflake.
- Du kan aktivera användarna att automatiskt få loggat in på Snowflake (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Snowflake, behöver du följande objekt:

- En Azure AD-prenumeration
- En Snowflake enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Snowflake från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-snowflake-from-the-gallery"></a>Att lägga till Snowflake från galleriet

För att konfigurera integrering av Snowflake i Azure AD, som du behöver lägga till Snowflake från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Snowflake från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Snowflake**väljer **Snowflake** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Snowflake i resultatlistan](./media/snowflake-tutorial/tutorial_snowflake_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Snowflake baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Snowflake är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Snowflake upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Snowflake, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa Snowflake-testanvändare](#creating-snowflake-test-user)**  – du har en motsvarighet för Britta Simon i Snowflake som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Snowflake-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Snowflake:**

1. I Azure-portalen på den **Snowflake** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. På den **SAML grundkonfiguration** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Snowflake-domän och URL: er med enkel inloggning för information](./media/snowflake-tutorial/tutorial_snowflake_url1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com/fed/login`

5. Klicka på **ange ytterligare webbadresser** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Snowflake-domän och URL: er med enkel inloggning för information](./media/snowflake-tutorial/tutorial_snowflake_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<SNOWFLAKE-URL>.snowflakecomputing.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL.

6. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att hämta **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/snowflake-tutorial/tutorial_snowflake_certificate.png) 

7. På den **konfigurera Snowflake** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Azure AD-ID

    c. Utloggnings-URL

    ![Snowflake-konfiguration](common/configuresection.png)

8. I ett annat webbläsarfönster, logga in på Snowflake som en administratör.

9. **Växla rollen** till **ACCOUNTADMIN**, genom att klicka på **profil** på upp till höger på sidan.

    > [!NOTE]
    > Detta skiljer sig från den kontext som du har valt i det övre högra hörnet under ditt användarnamn
    
    ![Snowflake-administratör ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

10. Öppna den **ned Base64-certifikat** i anteckningar. Kopiera värdet mellan ”---BEGIN CERTIFICATE---” och ”---END CERTIFICATE---” och klistra in i de dubbla citattecknen bredvid **certifikat** nedan. I den **ssoUrl**, klistra in **inloggnings-URL** värde som du har kopierat från Azure-portalen. Välj den **alla frågor** och klicka på **kör**.

    ![Snowflake-sql](./media/snowflake-tutorial/tutorial_snowflake_sql.png)

    ```
    use role accountadmin;
    alter account set saml_identity_provider = '{
    "certificate": "<Paste the content of downloaded certificate from Azure portal>",
    "ssoUrl":"<Login URL value which you have copied from the Azure portal>",
    "type":"custom",
    "label":"AzureAD"
    }';
    alter account set sso_login_page = TRUE;
    ```

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-snowflake-test-user"></a>Skapa Snowflake testanvändare

Om du vill aktivera Azure AD-användare att logga in på Snowflake, måste de etableras i Snowflake. I Snowflake är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på Snowflake som en administratör.

2. **Växla rollen** till **ACCOUNTADMIN**, genom att klicka på **profil** på upp till höger på sidan.  

    ![Snowflake-administratör ](./media/snowflake-tutorial/tutorial_snowflake_accountadmin.png)

3. Skapa användaren genom att köra den nedan SQL-fråga, att se till att ”inloggningsnamn” anges till Azure AD-användarnamn i kalkylbladet som visas nedan.

    ![Snowflake-adminsql ](./media/snowflake-tutorial/tutorial_snowflake_usersql.png)

    ```
    use role accountadmin;
    CREATE USER britta_simon PASSWORD = '' LOGIN_NAME = 'BrittaSimon@contoso.com' DISPLAY_NAME = 'Britta Simon';
    ```

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Snowflake.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Snowflake**.

    ![Konfigurera enkel inloggning](./media/snowflake-tutorial/tutorial_snowflake_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Snowflake i åtkomstpanelen du bör få automatiskt loggat in på ditt Snowflake-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
