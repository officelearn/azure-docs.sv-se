---
title: 'Självstudier: Azure Active Directory-integration med Veracode | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 2dd273a1a0b5a5af65c4c40337975cb6f3f858ae
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049838"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Självstudier: Azure Active Directory-integration med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (AD Azure).

Integrera Veracode med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Veracode.
- Du kan aktivera användarna att automatiskt få loggat in på Veracode (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Veracode, behöver du följande objekt:

- En Azure AD-prenumeration
- En Veracode enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Veracode från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-veracode-from-the-gallery"></a>Lägg till Veracode från galleriet
För att konfigurera integrering av Veracode i Azure AD, som du behöver lägga till Veracode från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Veracode från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Veracode**väljer **Veracode** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Veracode i resultatlistan](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Veracode baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Veracode är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Veracode upprättas.

I Veracode, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Veracode, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Veracode](#create-a-veracode-test-user)**  – du har en motsvarighet för Britta Simon i Veracode som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Veracode program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Veracode:**

1. I Azure-portalen på den **Veracode** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

3. På den **Veracode domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure. 

    ![Konfigurera enkel inloggning](./media/veracode-tutorial/tutorial_veracode_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

5. Målet med det här avsnittet som beskriver hur du aktiverar användare att autentisera till Veracode med ett konto i Azure AD med federation baserat på SAML-protokoll.

    Programmets Veracode förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar till din **saml-tokenattribut** konfiguration. Följande skärmbild visar ett exempel för detta.
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_attr.png "attribut")

6. Om du vill lägga till de nödvändiga attributmappningarna, utför du följande steg:

    | Attributnamn | Attributvärde |
    |--- |--- |
    | Förnamn |User.givenName |
    | Efternamn |User.surname |
    | e-post |User.Mail |
    
    a. För varje datarad i tabellen ovan, klickar du på **lägga till användarattribut**.
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_addattr.png "attribut")
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_addattr1.png "attribut")
    
    b. I den **attributnamnet** textrutan skriver du attributnamnet som visas för den raden.
    
    c. I den **attributvärdet** textrutan väljer attribut-värde som visas för den raden.
    
    d. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/veracode-tutorial/tutorial_general_400.png)

8. På den **Veracode Configuration** klickar du på **konfigurera Veracode** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Veracode konfiguration](./media/veracode-tutorial/tutorial_veracode_configure.png) 

9. Logga in på webbplatsen Veracode företag som en administratör i ett annat webbläsarfönster.

10. Klicka på menyn längst upp **inställningar**, och klicka sedan på **Admin**.
   
    ![Administration](./media/veracode-tutorial/ic802911.png "Administration")

11. Klicka på den **SAML** fliken.

12. I den **SAML organisationsinställningar** avsnittet, utför följande steg:
   
    ![Administration](./media/veracode-tutorial/ic802912.png "Administration")
   
    a.  I **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
    
    b. Om du vill ladda upp nedladdade certifikatet från Azure-portalen, klickar du på **Välj fil**.
   
    c. Välj **aktivera självregistrering**.

13. I den **Self Registreringsinställningar** avsnittet, utför följande steg och klicka sedan på **spara**:
   
    ![Administration](./media/veracode-tutorial/ic802913.png "Administration")
   
    a. Som **aktivera för nya användare**väljer **ingen aktivering krävs**.
   
    b. Som **användardata uppdateras**väljer **inställning Veracode användardata**.
   
    c. För **SAML attributinformation**, väljer du följande:
      * **Användarroller**
      * **Princip för administratör**
      * **Granskare**
      * **Security Lead**
      * **Chef**
      * **Jobbets avsändare**
      * **Skapare**
      * **Alla typer av genomgångar**
      * **Team-medlemskap**
      * **Standard-teamet**

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/veracode-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/veracode-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/veracode-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/veracode-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-veracode-test-user"></a>Skapa en Veracode testanvändare
För att aktivera Azure AD-användare att logga in på Veracode, måste de etableras i Veracode. När det gäller Veracode är etablering en automatisk uppgift. Det finns inga uppgift åt dig. Användare skapas automatiskt om det behövs under det första enkla inloggning för försöket.

> [!NOTE]
> Du kan använda alla andra Veracode användare konto verktyg för att skapa eller API: er som tillhandahålls av Veracode att etablera användarkonton i Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Veracode.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Veracode, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Veracode**.

    ![Länken Veracode i listan med program](./media/veracode-tutorial/tutorial_veracode_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Veracode i åtkomstpanelen du bör få automatiskt loggat in på ditt Veracode program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

