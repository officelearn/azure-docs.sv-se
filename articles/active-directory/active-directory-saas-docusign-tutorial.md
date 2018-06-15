---
title: 'Självstudier: Azure Active Directory-integrering med DocuSign | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: jeedes
ms.openlocfilehash: 28c46e7071841552c5bcbf3df38d9d7771b0cfe6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345756"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Självstudier: Azure Active Directory-integrering med DocuSign

I kursen får lära du att integrera DocuSign med Azure Active Directory (AD Azure).

Integrera DocuSign med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till DocuSign
- Du kan aktivera användarna att automatiskt hämta loggat in på DocuSign (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med DocuSign, behöver du följande:

- En Azure AD-prenumeration
- En DocuSign enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till DocuSign från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-docusign-from-the-gallery"></a>Att lägga till DocuSign från galleriet
Du måste lägga till DocuSign från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av DocuSign i Azure AD.

**Utför följande steg för att lägga till DocuSign från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **DocuSign**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_search.png)

5. Välj i resultatpanelen **DocuSign**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med DocuSign baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i DocuSign motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i DocuSign upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i DocuSign.

Om du vill konfigurera och testa Azure AD enkel inloggning med DocuSign, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare DocuSign](#creating-a-docusign-test-user)**  – du har en motsvarighet för Britta Simon i DocuSign som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt DocuSign program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med DocuSign:**

1. I Azure-portalen på den **DocuSign** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_samlbase.png)

3. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base 64)** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_certificate.png) 

4. På den **DocuSign Configuration** på Azure portal, klicka på **konfigurera DocuSign** öppna Konfigurera inloggning fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_configure.png)

5. I en annan webbläsarfönstret, logga in på ditt **DocuSign administrationsportalen** som administratör.

6. Klicka på navigeringsmenyn till vänster **domäner**.
   
    ![Konfigurerar enkel inloggning][51]

7. I den högra rutan, klickar du på **anspråk domän**.
   
    ![Konfigurerar enkel inloggning][52]

8. På den **anspråk en domän** dialogrutan i den **domännamn** textruta Skriv företagets domän och klicka sedan på **anspråk**. Se till att du verifiera domänen och att statusen är aktiv.
   
    ![Konfigurerar enkel inloggning][53]

9. Klicka på menyn till vänster **identitetsleverantörer**  
   
    ![Konfigurerar enkel inloggning][54]
10. I den högra rutan, klickar du på **lägga till identitetsleverantör**. 
   
    ![Konfigurerar enkel inloggning][55]

11. På den **identitet providerinställningar** utför följande steg:
   
    ![Konfigurerar enkel inloggning][56]

    a. I den **namn** textruta, ange ett unikt namn för din konfiguration. Använd inte blanksteg.

    b. Klistra in **SAML enhets-ID** till den **identitet providern utfärdaren** textruta.

    c. Klistra in **SAML enkel inloggning Tjänstwebbadress** till den **identitet providern inloggnings-URL** textruta.

    d. Klistra in **Sign-Out URL** till den **identitet providern logga ut URL** textruta.

    e. Välj **logga AuthN begäran**.

    f. Som **skicka AuthN-begäran från**väljer **efter**.

    g. Som **skicka logga ut begäran från**väljer **hämta**.

12. I den **anpassad attributmappning** väljer du de fält som du vill mappa med Azure AD-anspråk. I det här exemplet i **emailaddress** anspråk mappas med värdet för **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Det är standardnamnet för anspråk från Azure AD för e-anspråk. 
   
    > [!NOTE]
    > Använd rätt **användar-ID** att mappa användaren från Azure AD att DocuSign mappning. Välj rätt fältet och ange rätt värde baserat på dina organisationsinställningar.
          
    ![Konfigurerar enkel inloggning][57]

13. I den **providern identitetscertifikat** klickar du på **Lägg till certifikat**, och sedan ladda upp det certifikat som du har hämtat från Azure AD-portalen.   
   
    ![Konfigurerar enkel inloggning][58]

14. Klicka på **Spara**.

15. I den **identitetsleverantörer** klickar du på **åtgärder**, och klicka sedan på **slutpunkter**.   
   
    ![Konfigurerar enkel inloggning][59]
 
16. I den **visa SAML 2.0 slutpunkter** avsnittet på **DocuSign administrationsportal**, utför följande steg:
   
    ![Konfigurerar enkel inloggning][60]
   
    a. Kopiera den **Service Provider utfärdar-URL**, och sedan klistra in i den **identifierare** textruta på **DocuSign domän och URL: er** på Azure portal följer mönstret: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/login/sp/<uniqueID>`.
   
    b. Kopiera den **Service Provider inloggnings-URL**, och sedan klistra in i den **logga URL** textruta på **DocuSign domän och URL: er** på Azure portal följer mönstret: `https://<subdomain>.docusign.com/organization/<uniqueID>/saml2/`.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_url.png)
      
    c.  Klicka på **Stäng**
    
17. Klicka på Azure-portalen **spara**.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_general_400.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-docusign-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-docusign-test-user"></a>Skapa en testanvändare DocuSign

Programmet stöder **precis i tid användaretablering** och efter autentisering användare skapas automatiskt i programmet.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till DocuSign.

![Tilldela användare][200] 

**Om du vill tilldela DocuSign Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **DocuSign**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-docusign-tutorial/tutorial_docusign_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen DocuSign på åtkomstpanelen du bör få automatiskt loggat in på ditt DocuSign program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
* [Konfigurera Användaretablering](active-directory-saas-docusign-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_04.png
[51]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/active-directory-saas-docusign-tutorial/tutorial_docusign_29.png
[100]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-docusign-tutorial/tutorial_general_203.png

