---
title: "Självstudier: Azure Active Directory-integrering med Veritas Enterprise Vault.cloud SSO | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veritas Enterprise Vault.cloud enkel inloggning."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2017
ms.author: jeedes
ms.openlocfilehash: eb9243367d0817d37549fa147c6c5e1d2acf3761
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="tutorial-azure-active-directory-integration-with-veritas-enterprise-vaultcloud-sso"></a>Självstudier: Azure Active Directory-integrering med Veritas Enterprise Vault.cloud enkel inloggning

I kursen får lära du att integrera Veritas Enterprise Vault.cloud enkel inloggning med Azure Active Directory (AD Azure).

Integrera Veritas Enterprise Vault.cloud enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Veritas Enterprise Vault.cloud SSO
- Du kan aktivera användarna att automatiskt hämta loggat in på Veritas Enterprise Vault.cloud SSO (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Veritas Enterprise Vault.cloud enkel inloggning, behöver du följande:

- En Azure AD-prenumeration
- En Veritas Enterprise Vault.cloud SSO enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Veritas Enterprise Vault.cloud SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-veritas-enterprise-vaultcloud-sso-from-the-gallery"></a>Att lägga till Veritas Enterprise Vault.cloud SSO från galleriet
Du måste lägga till Veritas Enterprise Vault.cloud SSO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Veritas Enterprise Vault.cloud SSO i Azure AD.

**Utför följande steg för att lägga till Veritas Enterprise Vault.cloud SSO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Veritas Enterprise Vault.cloud SSO**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_search.png)

5. Välj i resultatpanelen **Veritas Enterprise Vault.cloud SSO**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Veritas Enterprise Vault.cloud SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren Veritas Enterprise Vault.cloud SSO till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren Veritas Enterprise Vault.cloud SSO upprättas.

Veritas Enterprise Vault.cloud SSO, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Veritas Enterprise Vault.cloud enkel inloggning måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Veritas Enterprise Vault.cloud SSO](#creating-a-veritas-enterprise-vaultcloud-sso-test-user)**  – du har en motsvarighet för Britta Simon Veritas Enterprise Vault.cloud SSO som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Veritas Enterprise Vault.cloud SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Veritas Enterprise Vault.cloud enkel inloggning:**

1. I Azure-portalen på den **Veritas Enterprise Vault.cloud SSO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_samlbase.png)

3. På den **Veritas företagsdomänen Vault.cloud SSO och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://personal.ap.archive.veritas.com/CID=<CUSTOMERID>`

    b. I den **identifierare** textruta använder URL-Adressen i datacentret

    | Datacenter| Webbadress |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|

    c. I den **Reply URL** textruta använder URL-Adressen i datacentret

    | Datacenter| Webbadress |
    |----------|----|
    | Nordamerika| `https://auth.lax.archivecloud.net` |
    | Europa | `https://auth.ams.archivecloud.net` |
    | Asien och stillahavsområdet| `https://auth.syd.archivecloud.net`|
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Veritas Enterprise Vault.cloud klientfilerna supportteamet](https://www.veritas.com/support/.html) att hämta det här värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_general_400.png)

6. På den **Veritas företagskonfiguration Vault.cloud SSO** klickar du på **konfigurera Veritas Enterprise Vault.cloud SSO** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_configure.png) 

7. Konfigurera enkel inloggning på **Veritas Enterprise Vault.cloud SSO** sida, måste du skicka den hämtade **Certificate(Base64)** och **SAML enkel inloggning Tjänstwebbadress** till [Veritas Enterprise Vault.cloud SSO supportteamet](https://www.veritas.com/support/.html).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-veritas-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-veritas-enterprise-vaultcloud-sso-test-user"></a>Skapa en testanvändare Veritas Enterprise Vault.cloud SSO

I det här avsnittet skapar du en användare som kallas Britta Simon Enterprise Vault.cloud sso. Arbeta med [Veritas Enterprise Vault.cloud SSO supportteamet](https://www.veritas.com/support/.html) att lägga till användare i företaget Vault.cloud SSO-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Veritas Enterprise Vault.cloud enkel inloggning.

![Tilldela användare][200] 

**Om du vill tilldela Veritas Enterprise Vault.cloud SSO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Veritas Enterprise Vault.cloud SSO**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-veritas-tutorial/tutorial_veritas_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Veritas Enterprise Vault.cloud SSO på åtkomstpanelen du bör få automatiskt loggat in på ditt Veritas Enterprise Vault.cloud SSO-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-veritas-tutorial/tutorial_general_203.png

