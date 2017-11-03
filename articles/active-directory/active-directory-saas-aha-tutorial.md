---
title: "Självstudier: Azure Active Directory-integrering med Aha! | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Aha!."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 7723864b2e1ab2d5b69d86f0fa18416b9d3f9aa3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-aha"></a>Självstudier: Azure Active Directory-integrering med Aha!

Lär dig hur du integrerar Aha i den här självstudiekursen! med Azure Active Directory (AD Azure).

Integrera Aha! med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Aha!
- Du kan aktivera användarna att automatiskt hämta loggat in på Aha! (Enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Konfigurera Azure AD-integrering med Aha!, behöver du följande:

- En Azure AD-prenumeration
- En Aha! enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Aha! från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-aha-from-the-gallery"></a>Lägga till Aha! från galleriet
Så här konfigurerar du integrering av Aha! Du måste lägga till Aha till Azure AD! från galleriet i listan över hanterade SaaS-appar.

**Att lägga till Aha! Utför följande steg från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Aha!**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/tutorial_aha_search.png)

5. Välj i resultatpanelen **Aha!**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/tutorial_aha_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Aha! baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i Aha! är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Aha! måste upprättas.

I Aha!, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Konfigurera och testa Azure AD enkel inloggning med Aha!, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en Aha! testanvändare](#creating-an-aha-test-user)**  – har en motsvarighet för Britta Simon Aha! som är länkade till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Aha! Programmet.

**Konfigurera Azure AD enkel inloggning med Aha!, utför följande steg:**

1. I Azure-portalen på den **Aha!** programmet integrationssidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-aha-tutorial/tutorial_aha_samlbase.png)

3. På den **Aha! Domänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-aha-tutorial/tutorial_aha_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<companyname>.aha.io/session/new`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://<companyname>.aha.io`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Aha! Klienten supportteamet](https://www.aha.io/company/contact) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-aha-tutorial/tutorial_aha_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-aha-tutorial/tutorial_general_400.png)

6. Logga in på ditt Aha i ett annat webbläsarfönster! företagets webbplats som administratör.

7. Klicka på menyn högst upp **inställningar**.

    ![Inställningar för](./media/active-directory-saas-aha-tutorial/IC798950.png "inställningar")

8. Klicka på **konto**.
   
    ![Profilen](./media/active-directory-saas-aha-tutorial/IC798951.png "profil")

9. Klicka på **säkerhet och enkel inloggning**.
   
    ![Säkerhet och enkel inloggning](./media/active-directory-saas-aha-tutorial/IC798952.png "säkerhet och enkel inloggning")

10. I **enkel inloggning** avsnittet som **identitetsleverantör**väljer **SAML2.0**.
   
    ![Säkerhet och enkel inloggning](./media/active-directory-saas-aha-tutorial/IC798953.png "säkerhet och enkel inloggning")

11. På den **enkel inloggning** konfigurationen utför följande steg:
    
    ![Enkel inloggning](./media/active-directory-saas-aha-tutorial/IC798954.png "enkel inloggning")
    
       a. I den **namn** textruta, ange ett namn för din konfiguration.

       b. För **konfigurera med hjälp av**väljer **metadatafil**.
   
       c. Om du vill överföra din hämtade metadatafil klickar du på **Bläddra**.
   
       d. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-aha-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-aha-test-user"></a>Skapa en Aha! testanvändare

Aktivera Azure AD-användare kan logga in på Aha!, de måste etableras i Aha!.  

När det gäller Aha!, etablering är en automatisk uppgift. Det finns ingen åtgärd-objekt.

Användare skapas automatiskt om det behövs under det första enkla inloggning försöket.

>[!NOTE]
>Du kan använda andra Aha! Verktyg för att skapa användaren konto eller API: er som tillhandahålls av Aha! att tillhandahålla AAD användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Aha!.

![Tilldela användare][200] 

**Att tilldela Aha Britta Simon!, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Aha!**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-aha-tutorial/tutorial_aha_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-aha-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aha-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aha-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aha-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aha-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aha-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aha-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aha-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aha-tutorial/tutorial_general_203.png

