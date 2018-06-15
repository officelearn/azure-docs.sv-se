---
title: 'Självstudier: Azure Active Directory-integrering med Egnyte | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Egnyte.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: ea9cb831845a4f99929deac5bc4c63f8c53e5238
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34341213"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Självstudier: Azure Active Directory-integrering med Egnyte

I kursen får lära du att integrera Egnyte med Azure Active Directory (AD Azure).

Integrera Egnyte med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Egnyte
- Du kan aktivera användarna att automatiskt hämta loggat in på Egnyte (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Egnyte, behöver du följande:

- En Azure AD-prenumeration
- En Egnyte enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Egnyte från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-egnyte-from-the-gallery"></a>Att lägga till Egnyte från galleriet
Du måste lägga till Egnyte från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Egnyte i Azure AD.

**Utför följande steg för att lägga till Egnyte från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Egnyte**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_search.png)

5. Välj i resultatpanelen **Egnyte**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Egnyte baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Egnyte motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Egnyte upprättas.

I Egnyte, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Egnyte, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Egnyte](#creating-an-egnyte-test-user)**  – du har en motsvarighet för Britta Simon i Egnyte som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Egnyte program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Egnyte:**

1. I Azure-portalen på den **Egnyte** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_samlbase.png)

3. På den **Egnyte domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.egnyte.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Egnyte klienten supportteamet](https://www.egnyte.com/corp/contact_egnyte.html) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_general_400.png)

6. På den **Egnyte Configuration** klickar du på **konfigurera Egnyte** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen Egnyte företag som administratör.

8. Klicka på **inställningar**.
   
   ![Inställningar för](./media/active-directory-saas-egnyte-tutorial/ic787819.png "inställningar")

9. I menyn klickar du på **inställningar**.

   ![Inställningar för](./media/active-directory-saas-egnyte-tutorial/ic787820.png "inställningar")

10. Klicka på den **Configuration** fliken och klicka sedan på **säkerhet**.

    ![Säkerhet](./media/active-directory-saas-egnyte-tutorial/ic787821.png "säkerhet")

11. I den **autentisering för enkel inloggning** avsnittet, utför följande steg:

    ![Enkel inloggning för autentisering](./media/active-directory-saas-egnyte-tutorial/ic787822.png "enkel inloggning för autentisering")   
    
    a. Som **autentisering för enkel inloggning**väljer **SAML 2.0**.
   
    b. Som **identitetsleverantör**väljer **AzureAD**.
   
    c. Klistra in **SAML enkel inloggning Tjänstwebbadress** kopieras från Azure-portalen i den **identitet providern inloggnings-URL** textruta.
   
    d. Klistra in **SAML enhets-ID** som du har kopierat från Azure-portalen i den **identitet providern enhets-ID** textruta.
      
    e. Öppna din Base64-kodade certifikatet i anteckningar som hämtas från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textruta.
   
    f. Som **standard mappning**väljer **e-postadress**.
   
    g. Som **använder domänspecifika utfärdaren värdet**väljer **inaktiveras**.
   
    h. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-egnyte-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-egnyte-test-user"></a>Skapa en testanvändare Egnyte

Om du vill aktivera Azure AD-användare kan logga in på Egnyte etableras de i Egnyte. När det gäller Egnyte är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på ditt **Egnyte** företagets webbplats som administratör.

2. Gå till **inställningar \> användare och grupper**.

3. Klicka på **Lägg till nya användare**, och välj sedan typ av användare som du vill lägga till.
   
   ![Användare](./media/active-directory-saas-egnyte-tutorial/ic787824.png "användare")

4. I den **nya standardanvändare** avsnittet, utför följande steg:
   
   ![Nya standardanvändare](./media/active-directory-saas-egnyte-tutorial/ic787825.png "ny vanlig användare")   

   a. Typ av **e-post**, **användarnamn**, och annan information om ett giltigt Azure Active Directory-konto som du vill etablera.
   
   b. Klicka på **Spara**.
    
    >[!NOTE]
    >Azure Active Directory kontoinnehavaren får ett e-postmeddelande.
    >

>[!NOTE]
>Du kan använda något annat Egnyte användarens konto skapas verktyg eller API: er som tillhandahålls av Egnyte att etablera AAD-användarkonton.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Egnyte.

![Tilldela användare][200] 

**Om du vill tilldela Egnyte Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Egnyte**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-egnyte-tutorial/tutorial_egnyte_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Egnyte på åtkomstpanelen du bör få automatiskt loggat in på ditt Egnyte program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-egnyte-tutorial/tutorial_general_203.png

