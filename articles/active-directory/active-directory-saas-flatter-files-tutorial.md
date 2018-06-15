---
title: 'Självstudier: Azure Active Directory-integrering med plattare filer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och plattare filer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2017
ms.author: jeedes
ms.openlocfilehash: 2f2a068523f64eb381239560108c2a961da371be
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343218"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Självstudier: Azure Active Directory-integrering med plattare filer

I kursen får lära du att integrera plattare filer med Azure Active Directory (AD Azure).

Integrera plattare filer med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till plattare filer
- Du kan aktivera användarna att automatiskt hämta loggat in på plattare filer (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med plattare filer, behöver du följande:

- En Azure AD-prenumeration
- En plattare filer enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till plattare filer från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-flatter-files-from-the-gallery"></a>Lägga till plattare filer från galleriet
Du måste lägga till plattare filer från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av plattare filer i Azure AD.

**Utför följande steg för att lägga till plattare filer från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **plattare filer**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_search.png)

5. Välj i resultatpanelen **plattare filer**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med plattare filer baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i plattare filer till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i plattare filer upprättas.

I plattare filer, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med plattare filer, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare plattare filer](#creating-a-flatter-files-test-user)**  – du har en motsvarighet för Britta Simon i plattare filer som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet plattare filer.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med plattare filer:**

1. I Azure-portalen på den **plattare filer** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_samlbase.png)

3. På den **plattare filer domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_url.png)
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_general_400.png)

6. På den **plattare filer Configuration** klickar du på **konfigurera plattare filer** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_configure.png) 

7. Inloggning till plattare filer programmet som administratör.

8. Klicka på **INSTRUMENTPANELEN**. 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_05.png)  

9. Klicka på **inställningar**, och utför följande steg på den **företagets** fliken: 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Välj **använder SAML 2.0 för autentisering**.
    
    b. Klicka på **konfigurerar du SAML**.

8. På den **SAML-konfiguration** dialogrutan, utför följande steg: 
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. I den **domän** textruta, ange en registrerad domän.
   
    >[!NOTE]
    >Om du inte har en registrerad domän ännu, kontakta din plattare filer supportteam via [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. I **identitet providern URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat formuläret Azure-portalen.
   
    c.  Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textruta.

    d. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-flatter-files-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-flatter-files-test-user"></a>Skapa en testanvändare plattare filer

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i plattare filer.

**Utför följande steg för att skapa en användare som kallas Britta Simon i plattare filer:**

1. Logga in på ditt **plattare filer** företagets webbplats som administratör.

2. I navigeringsfönstret till vänster klickar du på **inställningar**, och klicka sedan på den **användare** fliken.
   
    ![Skapa en plattare filer användare](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klicka på **lägga till användare**. 

4. På den **Lägg till användare** dialogrutan, utför följande steg:
   
    ![Skapa en plattare filer användare](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. I den **Förnamn** textruta typen **Britta**.
   
    b. I den **efternamn** textruta typen **Simon**. 
   
    c. I den **e-postadress** textruta Skriv Brittas e-postadress i Azure-portalen.
   
    d. Klicka på **skicka**.   


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till plattare filer.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon plattare filer, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **plattare filer**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-flatter-files-tutorial/tutorial_flatterfiles_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen plattare filer på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet plattare filer.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-flatter-files-tutorial/tutorial_general_203.png

