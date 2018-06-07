---
title: 'Självstudier: Azure Active Directory-integrering med Pingboard | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 25777001907f705700d1c90e238d19a20242b50d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34590906"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Självstudier: Azure Active Directory-integrering med Pingboard

I kursen får lära du att integrera Pingboard med Azure Active Directory (AD Azure).

Integrera Pingboard med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Pingboard
- Du kan aktivera användarna att automatiskt hämta loggat in på Pingboard (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Pingboard, behöver du följande:

- En Azure AD-prenumeration
- En Pingboard enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Pingboard från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-pingboard-from-the-gallery"></a>Att lägga till Pingboard från galleriet
Du måste lägga till Pingboard från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Pingboard i Azure AD.

**Utför följande steg för att lägga till Pingboard från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Enterprise-program][2]

3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Pingboard**väljer **Pingboard** från resultatet Kontrollpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Pingboard i resultatlistan](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Pingboard baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Pingboard motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Pingboard upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Pingboard.

Om du vill konfigurera och testa Azure AD enkel inloggning med Pingboard, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Pingboard](#create-a-pingboard-test-user)**  – du har en motsvarighet för Britta Simon i Pingboard som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Pingboard program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Pingboard:**

1. I Azure-portalen på den **Pingboard** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2.  På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. På den **Pingboard domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Enkel inloggning information IDP Pingboard domän och URL: er](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. I den **identifierare** textruta Skriv värdet som: `http://app.pingboard.com/sp`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<entity-id>.pingboard.com/auth/saml/consume`

4. Kontrollera **visa avancerade inställningar för URL: en**, om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Pingboard domän med enkel inloggning information SP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     I den **inloggnings-URL** textruta Skriv URL-Adressen med följande mönster: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Observera att dessa värden inte är verkliga. Uppdatera dessa värden med den faktiska Reply URL och inloggnings-URL. Kontakta [Pingboard klienten supportteamet](https://support.pingboard.com/) att hämta dessa värden.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Pingboard xml-metadata](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. Konfigurera enkel inloggning på Pingboard sida, öppna ett nytt webbläsarfönster och logga in på ditt konto för Pingboard. Du måste vara en Pingboard-administratör för att konfigurera enkel inloggning.

8. På den översta menyn, Välj **appar > integreringar**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9. På den **integreringar** kan hitta den **”Azure Active Directory”** panelen och klicka på den.

    ![Pingboard Single Sign-On-integrering](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. I modal som följer Klicka **”konfigurera”**

    ![Pingboard configuration knappen](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. På sidan följande märker du att ”Azure SSO-integrering är aktiverad”. Öppna den hämta filen i XML-Metadata i en anteckningar och klistra in innehållet i **IDP Metadata**.

    ![Skärm för konfiguration av SSO Pingboard](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. Filen har verifierats och om allt stämmer enkel inloggning nu aktiveras.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png)

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png)

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="create-a-pingboard-test-user"></a>Skapa en testanvändare Pingboard

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Pingboard. Pingboard stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](active-directory-saas-pingboard-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt gör du följande:**

1. Logga in på webbplatsen Pingboard företag som administratör.

2. Klicka på **”Lägg till medarbetare”** knappen på **Directory** sidan.

    ![Lägga till medarbetare](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. På den **”Lägg till medarbetare”** dialogrutan utför följande steg:

    ![Bjud in personer](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. I den **fullständiga namn** textruta ange det fullständiga namnet för användare som **Britta Simon**.

    b. I den **e-post** textruta typen e-postadressen för användaren som **brittasimon@contoso.com**.

    c. I den **befattning** textruta skriver Britta Simon befattningen.

    d. I den **plats** listrutan, välj platsen för Britta Simon.

    e. Klicka på **Lägg till**.

4. En bekräftelseskärm visas för att bekräfta att lägga till användaren.

    ![Bekräfta](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Pingboard.

![Tilldela användare][200] 

**Om du vill tilldela Pingboard Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Pingboard**.

    ![Länken Pingboard i listan med program](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

När du klickar på panelen Pingboard på åtkomstpanelen du bör få automatiskt loggat in på ditt Pingboard program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](active-directory-saas-pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png