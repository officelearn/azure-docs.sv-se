---
title: 'Självstudier: Azure Active Directory-integrering med molnet för Microsoft Azure-hanteringsportalen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och molnet hanteringsportal för Microsoft Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 4ea9f47c-25ca-42b0-a878-9e7aa6f34973
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 5dfd5377fa669322d0a61c71996badd45bc3d441
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-cloud-management-portal-for-microsoft-azure"></a>Självstudier: Azure Active Directory-integrering med molntjänster Management Portal för Microsoft Azure

I kursen får lära du att integrera molnet hanteringsportal för Microsoft Azure med Azure Active Directory (AD Azure).

Integrera molnet hanteringsportal för Microsoft Azure med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till molnet Management Portal för Microsoft Azure
- Du kan aktivera användarna att automatiskt hämta loggat in på molnet hanteringsportal för Microsoft Azure (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med molntjänster Management Portal för Microsoft Azure, behöver du följande:

- En Azure AD-prenumeration
- En molnet Management Portal för Microsoft Azure enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till molnet hanteringsportal för Microsoft Azure från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cloud-management-portal-for-microsoft-azure-from-the-gallery"></a>Att lägga till molnet hanteringsportal för Microsoft Azure från galleriet
Du måste lägga till molnet hanteringsportal för Microsoft Azure från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av molnet hanteringsportal för Microsoft Azure i Azure AD.

**Utför följande steg för att lägga till molnet hanteringsportal för Microsoft Azure från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **molnet för Microsoft Azure-hanteringsportalen**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_search.png)

5. I resultatpanelen väljer **molnet för Microsoft Azure-hanteringsportalen**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med molnet hanteringsportal för Microsoft Azure baserad på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i molnet för Microsoft Azure-hanteringsportalen motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i molnet Management Portal för Microsoft Azure upprättas.

I molnet Management Portal för Microsoft Azure, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med molnet för Microsoft Azure-hanteringsportalen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en moln Management Portal för Microsoft Azure testanvändare](#creating-a-cloud-management-portal-for-microsoft-azure-test-user)**  – du har en motsvarighet för Britta Simon i molnet Management Portal för Microsoft Azure som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i molnet Management-portalen för Microsoft Azure-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med molnet för Microsoft Azure-hanteringsportalen:**

1. I Azure-portalen på den **moln hanteringsportal för Microsoft Azure** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_samlbase.png)

3. På den **moln hanteringsportal för URL: er och Microsoft Azure Domain** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: 
    
    | |
    |--|
    | `https://portal.newsignature.com/<instancename>` |   
    | `https://portal.igcm.com/<instancename>` |
    
    b. I den **identifierare** textruta Skriv en URL med följande mönster: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com` |
    | `https://<subdomain>.newsignature.com` |

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: 
    
    | |
    |--|
    | `https://<subdomain>.igcm.com/<instancename>` |
    | `https://<subdomain>.newsignature.com` |
    | `https://<subdomain>.newsignature.com/<instancename>` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [moln Management Portal för Microsoft Azure-klient supportteamet](mailto:jczernuszka@newsignature.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_general_400.png)

6. På den **moln hanteringsportal för Microsoft Azure Configuration** klickar du på **konfigurera Portal för molnet för Microsoft Azure** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_configure.png) 

7. Konfigurera enkel inloggning på **molnet för Microsoft Azure-hanteringsportalen** sida, måste du skicka den hämtade **certifikat**, **Sign-Out URL**, **SAML inloggning tjänst-URL för enkel** och **SAML enhets-ID** till [molnet hanteringsportal för Microsoft Azure-teamet](mailto:jczernuszka@newsignature.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-newsignature-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cloud-management-portal-for-microsoft-azure-test-user"></a>Skapa en moln Management Portal för Microsoft Azure testanvändare

Syftet med det här avsnittet är att skapa en användare som heter Britta Simon i molnet Management-portalen för Microsoft Azure. Se tillsammans med [moln hanteringsportal för Microsoft Azure-teamet](mailto:jczernuszka@newsignature.com) att lägga till användare i hanteringsportalen molnet för Microsoft Azure-konto.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till molnet för Microsoft Azure-hanteringsportalen.

![Tilldela användare][200] 

**Om du vill tilldela moln hanteringsportal för Microsoft Azure Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **molnet för Microsoft Azure-hanteringsportalen**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-newsignature-tutorial/tutorial_newsignature_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.
När du klickar på hanteringsportalen molnet för Microsoft Azure-panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt moln Management Portal för Microsoft Azure-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-newsignature-tutorial/tutorial_general_203.png

