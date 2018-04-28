---
title: 'Självstudier: Azure Active Directory-integrering med Netsuite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Netsuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2018
ms.author: jeedes
ms.openlocfilehash: ecbadd45f964e4d4d36dcf67cf85cd878f012150
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Självstudier: Azure Active Directory-integrering med Netsuite

I kursen får lära du att integrera Netsuite med Azure Active Directory (AD Azure).

Integrera Netsuite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Netsuite
- Du kan aktivera användarna att automatiskt hämta loggat in på Netsuite (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Netsuite, behöver du följande:

- En Azure AD-prenumeration
- En Netsuite enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Netsuite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-netsuite-from-the-gallery"></a>Att lägga till Netsuite från galleriet
Du måste lägga till Netsuite från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Netsuite i Azure AD.

**Utför följande steg för att lägga till Netsuite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Netsuite**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_search.png)

5. Välj i resultatpanelen **Netsuite**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Netsuite baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Netsuite motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Netsuite upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Netsuite.

Om du vill konfigurera och testa Azure AD enkel inloggning med Netsuite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Netsuite](#creating-a-netsuite-test-user)**  – du har en motsvarighet för Britta Simon i Netsuite som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Netsuite program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Netsuite:**

1. I Azure-portalen på den **Netsuite** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_samlbase.png)

3. På den **Netsuite domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_url.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster:   `https://<tenant-name>.netsuite.com/saml2/acs` `https://<tenant-name>.na1.netsuite.com/saml2/acs` `https://<tenant-name>.na2.netsuite.com/saml2/acs` `https://<tenant-name>.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na1.sandbox.netsuite.com/saml2/acs` `https://<tenant-name>.na2.sandbox.netsuite.com/saml2/acs`

    > [!NOTE] 
    > Detta är inte verkliga värden. Uppdatera dessa värden med den faktiska Reply-URL. Kontakta [Netsuite supportteamet](http://www.netsuite.com/portal/services/support.shtml) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_general_400.png)

6. På den **Netsuite Configuration** klickar du på **konfigurera Netsuite** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_configure.png) 

7. Öppna en ny flik i webbläsaren och logga in på webbplatsen Netsuite företag som administratör.

8. I verktygsfältet högst upp på sidan, klickar du på **installationsprogrammet**, klicka på **installationsprogrammet**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

9. Från den **installationsaktiviteter** väljer **integrering**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-integration.png)

10. I den **hantera autentisering** klickar du på **SAML enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-saml.png)

11. På den **SAML installationsprogrammet** utför följande steg:
   
    a. Kopiera den **SAML inloggning tjänst-URL för enkel** värde från **Snabbreferens** avsnitt i **konfigurera inloggning** och klistrar in det i den **identitet providern inloggningssidan** i Netsuite.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/ns-saml-setup.png)
  
    b. Välj i Netsuite, **primära autentiseringsmetod**.

    c. För fältet **SAMLV2 identitet providern Metadata**väljer **överför IDP metadatafil**. Klicka på **Bläddra** att överföra metadatafilen som du hämtade från Azure-portalen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/ns-sso-setup.png)

    d. Klicka på **skicka**.

12. I Azure AD, klickar du på **visa och redigera andra användarattribut** kryssrutan och Lägg till attribut.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-attributes.png)

13. För den **attributnamn** anger i `account`. För den **attributvärdet** anger i din Netsuite konto-ID. Det här värdet är konstant och ändra med kontot. Instruktioner om hur du hittar konto-ID finns nedan:

      ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-add-attribute.png)

    a. Klicka på Netsuite, **installationsprogrammet** på menyn övre navigeringsfältet.

    b. Klicka på under den **installationsaktiviteter** avsnitt i den vänstra navigeringsfönstret menyn och väljer den **integrering** avsnittet och klicka på **Web Services-inställningar**.

    c. Kopiera Netsuite konto-ID och klistrar in det i den **attributvärdet** i Azure AD.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-account-id.png)

14. Innan användarna kan utföra enkel inloggning till Netsuite, måste de först tilldelas behörighet i Netsuite. Följ anvisningarna nedan för att tilldela dessa behörigheter.

    a. Klicka på menyn övre navigeringsfältet **installationsprogrammet**, klicka på **installationsprogrammet**.
      
      ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    b. Välj på den vänstra navigeringsmenyn **användare och roller för**, klicka på **hantera roller**.
      
      ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-manage-roles.png)

    c. Klicka på **ny roll**.

    d. Ange en **namn** för den nya rollen.
      
      ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-new-role.png)

    e. Klicka på **Spara**.

    f. Klicka på menyn högst upp **behörigheter**. Klicka på **installationsprogrammet**.
      
       ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-sso.png)

    g. Välj **ställa in SAML enkel inloggning**, och klicka sedan på **Lägg till**.

    h. Klicka på **Spara**.

    i. Klicka på menyn övre navigeringsfältet **installationsprogrammet**, klicka på **installationsprogrammet**.
      
       ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-setup.png)

    j. Välj på den vänstra navigeringsmenyn **användare och roller för**, klicka på **hantera användare**.
      
       ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-manage-users.png)

    k. Välj en testanvändare. Klicka på **redigera**.
      
       ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-edit-user.png)

    l. Markera den roll som du har skapat och klicka på dialogrutan roller **Lägg till**.
      
       ![Konfigurera enkel inloggning](./media/active-directory-saas-Netsuite-tutorial/ns-add-role.png)

    m. Klicka på **Spara**.
    
### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_01.png) 

2.  Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-netsuite-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 

### <a name="creating-a-netsuite-test-user"></a>Skapa en testanvändare Netsuite

I det här avsnittet skapas en användare som kallas Britta Simon i Netsuite. Netsuite stöder just-in-time-allokering som är aktiverad som standard.
Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i Netsuite, skapas en ny när du försöker komma åt Netsuite.


### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Netsuite.

![Tilldela användare][200] 

**Om du vill tilldela Netsuite Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Netsuite**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-netsuite-tutorial/tutorial_netsuite_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen på [ https://myapps.microsoft.com ](https://myapps.microsoft.com/), logga in på kontot test och på **Netsuite**.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-netsuite-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-netsuite-tutorial/tutorial_general_203.png

