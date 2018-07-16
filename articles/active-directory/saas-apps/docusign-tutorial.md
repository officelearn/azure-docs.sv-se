---
title: 'Självstudier: Azure Active Directory-integration med DocuSign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 05ec113db5fbdc0f2ea7d1f176c9be654f53a946
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053350"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Självstudier: Azure Active Directory-integration med DocuSign

I den här självstudien får du lära dig hur du integrerar DocuSign med Azure Active Directory (AD Azure).

Integrera DocuSign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till DocuSign
- Du kan aktivera användarna att automatiskt få loggat in på DocuSign (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med DocuSign, behöver du följande objekt:

- En Azure AD-prenumeration
- En DocuSign enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till DocuSign från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-docusign-from-the-gallery"></a>Att lägga till DocuSign från galleriet
För att konfigurera integrering av DocuSign i Azure AD, som du behöver lägga till DocuSign från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till DocuSign från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **DocuSign**.

    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/tutorial_docusign_search.png)

5. I resultatpanelen väljer **DocuSign**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med DocuSign baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i DocuSign är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i DocuSign upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i DocuSign.

Om du vill konfigurera och testa Azure AD enkel inloggning med DocuSign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare DocuSign](#creating-a-docusign-test-user)**  – du har en motsvarighet för Britta Simon i DocuSign som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt DocuSign-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med DocuSign:**

1. I Azure-portalen på den **DocuSign** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_samlbase.png)

3. På den **SAML-signeringscertifikat** klickar du på **certifikat (base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_certificate.png) 

4. På den **DocuSign Configuration** på Azure portal, klicka på **konfigurera DocuSign** öppna Konfigurera inloggnings-fönstret. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**
    
    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_configure.png)

5. I ett annat webbläsarfönster, logga in på din **DocuSign administrationsportalen** som administratör.

6. På navigeringsmenyn till vänster, klickar du på **domäner**.
   
    ![Konfigurerar enkel inloggning][51]

7. I den högra rutan klickar du på **anspråk domän**.
   
    ![Konfigurerar enkel inloggning][52]

8. På den **gör anspråk på en domän** dialogrutan i den **domännamn** textruta, ange din företagsdomän och klicka sedan på **anspråk**. Se till att du verifiera domänen och att statusen är aktiv.
   
    ![Konfigurerar enkel inloggning][53]

9. I menyn till vänster, klickar du på **Identitetsprovidrar**  
   
    ![Konfigurerar enkel inloggning][54]
10. I den högra rutan klickar du på **Lägg till identitetsprovider**. 
   
    ![Konfigurerar enkel inloggning][55]

11. På den **identitet providerinställningar** utför följande steg:
   
    ![Konfigurerar enkel inloggning][56]

    a. I den **namn** textrutan skriver du ett unikt namn för din konfiguration. Använd inte blanksteg.

    b. Klistra in **SAML entitets-ID** till den **Providerutgivare** textrutan.

    c. Klistra in **SAML enkel inloggning för tjänst-URL** till den **inloggnings-URL för identitetsprovider** textrutan.

    d. Klistra in **URL: en för utloggning** till den **utloggnings-URL för identitetsprovider** textrutan.

    e. Välj **logga AuthN begäran**.

    f. Som **skicka AuthN-begäran från**väljer **POST**.

    g. Som **skicka utloggningsbegäran från**väljer **hämta**.

12. I den **anpassad attributmappning** väljer du fältet som du vill mappa med Azure AD-anspråk. I det här exemplet på **e-postadress** anspråk har mappats med värdet för **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Det är standardnamnet för anspråk från Azure AD för e-anspråk. 
   
    > [!NOTE]
    > Använd lämplig **användaridentifierare** att mappa användaren från Azure AD att DocuSign användarmappning. Välj rätt fältet och ange rätt värde baserat på Organisationsinställningarna för din.
          
    ![Konfigurerar enkel inloggning][57]

13. I den **providern identitetscertifikat** klickar du på **Lägg till certifikat**, och sedan ladda upp det certifikat som du har hämtat från Azure AD-portalen.   
   
    ![Konfigurerar enkel inloggning][58]

14. Klicka på **Spara**.

15. I den **Identitetsprovidrar** klickar du på **åtgärder**, och klicka sedan på **slutpunkter**.   
   
    ![Konfigurerar enkel inloggning][59]
 
16. I den **visa SAML 2.0 slutpunkter** avsnittet på **DocuSign administrationsportalen**, utför följande steg:
   
    ![Konfigurerar enkel inloggning][60]
   
    a. Kopiera den **utfärdar-URL för Service Provider**, och sedan klistra in i den **identifierare** textrutan på **DocuSign domän och URL: er** på Azure portal följer mönstret: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopiera den **inloggnings-URL för tjänsten**, och sedan klistra in i den **inloggning på URL: en** textrutan på **DocuSign domän och URL: er** på Azure portal följer mönstret: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/` .

    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Klicka på **Stäng**
    
17. På Azure-portalen klickar du på **spara**.
    
    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/create_aaduser_02.png) 

3. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/docusign-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-docusign-test-user"></a>Skapa en testanvändare DocuSign

Program stöder **Just-in-time användaretablering** och när autentiserade användare skapas automatiskt i programmet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst DocuSign.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon DocuSign, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **DocuSign**.

    ![Konfigurera enkel inloggning](./media/docusign-tutorial/tutorial_docusign_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen DocuSign i åtkomstpanelen du bör få automatiskt loggat in på ditt DocuSign-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/docusign-tutorial/tutorial_general_01.png
[2]: ./media/docusign-tutorial/tutorial_general_02.png
[3]: ./media/docusign-tutorial/tutorial_general_03.png
[4]: ./media/docusign-tutorial/tutorial_general_04.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/docusign-tutorial/tutorial_general_100.png

[200]: ./media/docusign-tutorial/tutorial_general_200.png
[201]: ./media/docusign-tutorial/tutorial_general_201.png
[202]: ./media/docusign-tutorial/tutorial_general_202.png
[203]: ./media/docusign-tutorial/tutorial_general_203.png

